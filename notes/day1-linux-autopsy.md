Dissecting Processes (The "What is running?")
Malware often tries to hide by naming itself something boring, like systemd-helper. We need to spot the imposters.
PID 1 on my machine is /sbin/init splash. This is the initialization process that starts everything else.
Since I am using a Ubuntu 22.04 machine, its probably a mask(a symbolic link)for compatibility with older software. It is actually systemd.
This was verified when I ran the commnand: ls -l /sbin/init.
The output was:
/sbin/init -> ../lib/systemd/systemd
The command-line arguement splash tells the system to display the graphical loading screen during boot.

Dissecting the Network (The "Who is talking?")
Security is mostly just "networking with malice". You need to see what doors are open on your machine.
After running: sudo ss -tupln.
tcp            LISTEN          0               4096                          127.0.0.1:34067                          0.0.0.0:*              users:(("containerd",pid=1683,fd=10))               
I found containerd listening to port: 34067.
Containerd manages the lifecycle of docker containers.
127.0.0.1:34067 is the docker container running on localhost. This service only listen to the computer its running on. No one can see this port, its invisible to the outside world. It is secure because it is bound to the loopback interface.
If I saw 0.0.0.0:34067, this would mean that the service is listening on all interfaces (Ethernet, Wi-Fi, etc) and would create an attack surface.

Dissecting Permissions (The "Who is allowed?")
In cloud security, 90% of breaches are misconfigurations—specifically, giving too much permission. In Linux, "everything is a file." If permissions are wrong, users can steal secrets (like API keys).
Chmod 600 allows only me to read/write files. -rw-------.
Chmod 777 makes the files readable, writable, and executable by everyone. -rwxrwxrwx.
SSH keys must be set to 600. If they were set to 777, SSH would refuse to work because its too insecure.