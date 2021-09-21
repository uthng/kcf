WRAPF
===

`wrapf` is a small script wrapping `kubectl`, `kustomize`, `terraform` with `Fzf` to allow you to select one or multiple resources and perform an action on them.

![wrapf Demo](./images/kcf_0.2.0.gif)

For `kubectl`, you use `wrapf` in the same way as you use `kubectl` because `wrapf` uses **almost** the same syntax, same commands, same resource types and same options as `kubectl` does, except for several small differences as described in the usage section.


## Usage
```bash
Usage: wrapf <CMD> <TYPE> <OPTION1>...<OPTIONX> -- <ARG1> ... <ARGX>

CMD:       exec, logs, describe, delete, rollout, scale, autoscale, label, annotate, view, kb, ka, kd, tfwsa, tfwsp, tfwsd, tfwsr.
TYPE:      all kubectl resource types supported by the above commands.
OPTIONS:   all kubectl options normally used with the above commands & types.
ARGS:      command shell with its arguments to be executed in the container
```

### Kubernetes

Describe a pod in a given namespace:

```bash
$ wrapf describe pod -n istio-system
```

Execute a command shell in a container within a pod:
```bash
$ wrapf exec pod -n default -- ls -la
```

Execute a interactive shell in a container within a pod:
```
$ wrapf exec pod -n istio-system -it -- bash
```

Watch logs of a container within a pod in a given namespace:
```
$ wrapf logs pod -n istio-system -f
```

Execute a commanwrapfd shell in a container within a pod with `--all-namespaces`:

```bash
$ wrapf exec pod --all-namespaces -- ls -la
```

Delete a ressource:

```bash
$ wrapf delete secrets
```

View secret data entry:

```bash
$ wrapf view secrets -n mongodb
```

### Kustomize

Execute `kustomize build`:

```bash
$ wrapf kb ./envs
```

Execute `kustomize build` and `kubectl apply`:

```bash
$ wrapf ka ./envs
```

### Terraform


Execute `terraform plan` in a workspace that we are going to select in fuzzy:

```bash
$ wrapf tfwsa
```

Execute `terraform apply` in a workspace that we are going to select in fuzzy:

```bash
$ wrapf tfwsa
```

Execute `terraform destroy` in a workspace that we are going to select in fuzzy:

```bash
$ wrapf tfwsa
```

Remove a workspace that we are going to select in fuzzy:

```bash
$ wrapf tfwsr
```

## Installation
- **Manual:** MacOS, Linux and any POSIX environment with Bash/Zsh installed.
- **Homebrew:** soon and recommanded for MacOs.

### Manual

```bash
$ sudo git clone https://github.com/uthng/wrapf.git /opt/wrapf
$ chmod +x /opt/wrapf/wrapf
$ sudo ln -s /opt/wrapf/wrapf /usr/local/bin/wrapf
```

### Homebrew

Comming soon.

## How does it work ?

- For each command, `wrapf` first gets a list of resources corresponding to the given type (pods, deployment, pvc, etc) in the current namespace, in all namespaces or maybe with filters used commonly in `kubectl get`
- Then, `wrapf` passes it to `Fzf` to allow you to select one or multiple values depending on command type.
- Once a value selected, `wrapf` performs the given command with common `kubectl` options, if specified.
- If a container needs to be specified, `wrapf` does another `get` on the related pod to retrieve the container list and passes it to `Fzf`.
- If the command is not supported (neither be in kubectl command list nor in custom one), `kubectl` will be used as fallback.
- It works in the same way for `kustomize` or `terraform`.

***Notes:*** Not all but `wrapf`can be used with almost common commands, resource types, filters and options as you usually do with `kubectl`. The only thing you must respect is the syntax:
```
wrapf <CMD> <TYPE> <OPTION1>...<OPTIONX> -- <ARG1> ... <ARGX>
```
