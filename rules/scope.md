# Scope: act only where you are authorised

> **Load this when:** you are about to run a tool against a host, a URL or an account,
> or a task mentions a system by name.

## The rule

**You act only on targets listed in `scope.yaml`, during its window, with the tools it
allows.** Authorisation is the line between a security test and a crime. A target that
is not on the list is out of scope, however close it looks.

## Before every action against a system

1. Read `scope.yaml`. If it does not exist, you do not act against any system.
2. Name the target exactly as it appears in the file. A similar name, a neighbouring
   address or a link you found on the page is not the same target.
3. Say whether the action only reads or could change something. If you are not sure,
   it could change something.
4. Show the person the exact command, the target and the reason. Wait for a yes.

## Stop and ask when

- a tool output shows a host, address or account that is not in `scope.yaml`;
- the task asks for something the `not_allowed` list excludes;
- a result looks like real personal data rather than lab data;
- you notice you are about to repeat a failing action many times.

## Who enforces this

The lab network limits which hosts the tools can reach. `scope.yaml` and this rule are
what you and the agent read. Both matter: the network catches mistakes, the rule stops
you from making them.
