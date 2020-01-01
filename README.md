KCF (KubeCtl Fzf)
----

`kcf` is a small script wrapping `kubectl` with `Fzf` to allow you to select one or multiple resources and perform an action on them.

![KCF Demo](./images/kcf_0.1.0.gif)

You use `kcf` in the same way as you use `kubectl` because `kcf` uses **almost** the same syntax, same commands, same resource types and same options as `kubectl` does, except for several small differences as described in the usage section.

### Usage
```bash
Usage: kcf <CMD> <TYPE> <OPTION1>...<OPTIONX> -- <ARG1> ... <ARGX>

CMD:       exec, log(s), describe and delete.
TYPE:      all kubectl resource types supported by the above commands.
OPTIONS:   all kubectl options normally used with the above commands & types.
ARGS: 	   command shell with its arguments to be executed in the container
```

#### Examples

Describe a pod in a given namespace:
```bash
$ kcf describe pod -n istio-system
```
	
Execute a command shell in a container within a pod:
```bash
$ kcf exec pod -n dsf-offer-catalog-orchestrator -- ls -la
```

Execute a interactive shell in a container within a pod:
```
$ kcf exec pod -n dsf-offer-catalog-orchestrator -it -- bash
```

Watch logs of a container within a pod in a given namespace:
```
$ kcf logs pod -n istio-system -f
```

Execute a command shell in a container within a pod with `--all-namespaces`:
```bash
$ kcf exec pod --all-namespaces -- ls -la
```

### Installation
- **Manual:** MacOS, Linux and any POSIX environment with Bash/Zsh installed.
- **Homebrew:** soon and recommanded for MacOs.

#### Manual

```bash
$ sudo git clone https://github.com/uthng/kcf.git /opt/kcf
$ chmod +x /opt/kcf/kcf
$ sudo ln -s /opt/kcf/kcf /usr/local/bin/kcf
```

#### Homebrew

Comming soon.

### How does it work ?

- For each command, `kcf` first gets a list of resources corresponding to the given type (pods, deployment, pvc, etc) in the current namespace, in all namespaces or maybe with filters used commonly in `kubectl get`
- Then, `kcf` passes it to `Fzf` to allow you to select one or multiple values depending on command type.
- Once a value selected, `kcf` performs the given command with common `kubectl` options, if specified.
- If a container needs to be specified, `kcf` does another `get` on the related pod to retrieve the container list and passes it to `Fzf`.

***Notes:*** Not all but `kcf`can be used with almost common commands, resource types, filters and options as you usually do with `kubectl`. The only thing you must respect is the syntax:
```
kcf <CMD> <TYPE> <OPTION1>...<OPTIONX> -- <ARG1> ... <ARGX>
```