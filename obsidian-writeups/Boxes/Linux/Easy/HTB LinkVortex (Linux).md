failed this one, need to get better at enumerating git repos and to always try to identify a version of a running service to find exploits
also TOCTOU
## How to Identify a TOCTOU Vulnerability

A TOCTOU vulnerability exists when a program performs two distinct operations on a resource (like a file): first, it checks for a certain property of the resource, and second, it uses the resource. The vulnerability occurs because an attacker can change the resource in the small time window between the "check" and the "use."

Here's a mental checklist to use next time:

1. **Is there a privileged process?** Look for a script or binary that runs with elevated permissions (e.g., as `root` via `sudo` or a `suid` binary). Privileged execution is what makes the vulnerability dangerous.
    
2. **Does it operate on user-controlled input?** The process must act on something you can influence, like a file path you provide as an argument or a file you can upload.
    
3. **Is there a security check?** The script must perform some kind of validation on your input before proceeding. This is the "Time-of-Check."
    
4. **Is there a separate, privileged action?** After the check passes, the script must perform a sensitive action with the input, like reading, writing, or moving it. This is the "Time-of-Use."
    

The critical giveaway is the **separation between the check and the action**. If the script validates a file and then, in a separate step, uses that same file, a race condition is almost always possible.

## Clues Within the `clean_symlink.sh` Script

The script itself was full of red flags that pointed directly to this vulnerability. Let's break it down:

![[Pasted image 20250919022740.png]]

The script's logic is flawed because it reads the `LINK_TARGET` into a variable **only once**. It then makes a decision based on that initial check. Crucially, it **never re-verifies** the link's target before the `mv` or `cat` commands are executed. This gap is the vulnerability.

The moment you see a script check a file's property (like a symlink target) and then later perform an action on that file, you should immediately think, "Can I change the file in between those two steps?"

## Why is the `while` Loop "Faster" Than the Script?

This is a great question. The `while` loop isn't necessarily "faster" in terms of raw processing speed, but it is **more persistent and repetitive**. It exploits the way modern computers handle multitasking.

Here’s the concept:

- **Sequential Execution:** The `clean_symlink.sh` script executes its commands one after another. Between each line of the script, there is a minuscule but non-zero amount of time. There's a delay between the `readlink` command finishing and the `mv` command starting.
    
- **CPU Time Slicing:** Your operating system's kernel is constantly switching which process gets to use the CPU. It might give a few milliseconds to the `clean_symlink.sh` script, then a few milliseconds to your `while` loop, then back again.
    
- **Brute-Force Attack on Timing:** The `while true; do ln -sf /root/.ssh/id_rsa /var/quarantined/key.png; done` loop does one thing, over and over, as fast as the CPU will allow. It's like spamming a button.
    

By running this tight loop, you are creating a high probability that one of your `ln -sf` commands will be scheduled by the OS to run in the exact time slice **after** the script has checked the symlink but **before** it has moved and read it.

The race is between your `while` loop trying to change the link and the script trying to move on to the next command. Because your loop runs thousands of times, it's almost guaranteed to win that race eventually.