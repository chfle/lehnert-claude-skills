# /linux-selinux-helper

Diagnose and fix SELinux denials — file contexts, booleans, port labels, and custom policy modules. Never disables SELinux.

## Usage

```
/linux-selinux-helper <paste AVC denial or describe the problem>
```

## Examples

```
/linux-selinux-helper type=AVC msg=audit(1709123456.789:123): avc: denied { write } for pid=1234 comm="nginx" name="logs" dev="sda1" ino=56789 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:default_t:s0 tclass=dir permissive=0
/linux-selinux-helper nginx can't write to /data/logs — getting permission denied
/linux-selinux-helper my Python app on port 8443 is blocked by SELinux
/linux-selinux-helper httpd says "permission denied" connecting to PostgreSQL on localhost
/linux-selinux-helper sshd can't read /etc/ssh/authorized_keys in a custom location
/linux-selinux-helper show me commands to find what SELinux is currently blocking
/linux-selinux-helper write a custom SELinux policy module for my Go app in /opt/myapp
```

## Generates

```
selinux/
  <name>.te    ← custom Type Enforcement policy source (if custom module needed)
  <name>.pp    ← compiled binary policy module
  install.sh   ← semodule -i + verify commands
```

## Behavior

If an AVC denial (raw text from `audit.log` or `ausearch`) or a description is provided, diagnose immediately.
If no input is given, output the diagnostic commands to find active denials.

Always applies the **minimal correct fix** in this order:
1. **File context relabel** — `semanage fcontext` + `restorecon` (wrong context on a file/dir)
2. **Boolean toggle** — `setsebool -P` (common access pattern that has a ready-made boolean)
3. **Port label** — `semanage port -a` (app using non-standard port)
4. **Custom policy module** — `audit2allow` + manual `.te` review (when no built-in fix exists)

For each fix:
- Explain what the denial means in plain English
- Show the exact commands to apply the fix
- Show the verify command to confirm it worked
- Explain WHY the fix works (what the type/boolean/context does)

Never suggests `setenforce 0` or `SELINUX=disabled`. If the user asks to disable SELinux, explain the security implications and offer the minimal fix instead.

$ARGUMENTS
