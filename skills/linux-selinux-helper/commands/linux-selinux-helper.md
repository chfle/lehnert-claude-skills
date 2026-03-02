# /linux-selinux-helper

Diagnose and fix SELinux denials — file contexts, booleans, port labels, and custom policy modules.

## Usage

```
/linux-selinux-helper <paste AVC denial or describe the problem>
```

## Examples

```
/linux-selinux-helper [paste AVC denial from audit.log]
/linux-selinux-helper nginx can't write to /data/logs
/linux-selinux-helper my app on port 8443 is blocked by SELinux
/linux-selinux-helper httpd can't connect to my database
/linux-selinux-helper show me diagnostic commands to find what SELinux is blocking
/linux-selinux-helper write a custom policy module for my Go app
```

## Behavior

If an AVC denial or description is provided, diagnose immediately.
If no input is given, output the diagnostic commands to find what SELinux is blocking.

Always chooses the minimal correct fix in this order:
1. File context relabel (semanage fcontext + restorecon)
2. Boolean toggle (setsebool -P)
3. Port label (semanage port)
4. Custom .te policy module (audit2allow + manual review)

Never suggests disabling SELinux. Explains what the denial means and why the fix works.

$ARGUMENTS
