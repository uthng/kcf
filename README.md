KCF (KubeCtl Fzf)
===

`kcf` is a small script wrapping `kubectl`, `kustomize`, `terraform` with `Fzf` to allow you to select one or multiple resources and perform an action on them.

![KCF Demo](./images/kcf_0.2.0.gif)

For `kubectl`, you use `kcf` in the same way as you use `kubectl` because `kcf` uses **almost** the same syntax, same commands, same resource types and same options as `kubectl` does, except for several small differences as described in the usage section.


## Usage
```bash
Usage: kcf <CMD> <TYPE> <OPTION1>...<OPTIONX> -- <ARG1> ... <ARGX>

CMD:       exec, logs, describe, delete, rollout, scale, autoscale, label, annotate, view, kb, ka, kd, tfwsa, tfwsp, tfwsd, tfwsr.
TYPE:      all kubectl resource types supported by the above commands.
OPTIONS:   all kubectl options normally used with the above commands & types.
ARGS:      command shell with its arguments to be executed in the container
```

### Kubernetes

Describe a pod in a given namespace:

```bash
$ kcf describe pod -n istio-system
```

Execute a command shell in a container within a pod:
```bash
$ kcf exec pod -n default -- ls -la
```

Execute a interactive shell in a container within a pod:
```
$ kcf exec pod -n istio-system -it -- bash
```

Watch logs of a container within a pod in a given namespace:
```
$ kcf logs pod -n istio-system -f
```

Execute a command shell in a container within a pod with `--all-namespaces`:

```bash
$ kcf exec pod --all-namespaces -- ls -la
```

Delete a ressource:

```bash
$ kcf delete secrets
```

View secret data entry:

```bash
$ kcf view secrets -n mongodb
```

### Kustomize

Execute `kustomize build`:

```bash
$ kcf kb ./envs
```

Execute `kustomize build` and `kubectl apply`:

```bash
$ kcf ka ./envs
```

### Terraform


Execute `terraform plan` in a workspace that we are going to select in fuzzy:

```bash
$ kcf tfwsa
```

Execute `terraform apply` in a workspace that we are going to select in fuzzy:

```bash
$ kcf tfwsa
```

Execute `terraform destroy` in a workspace that we are going to select in fuzzy:

```bash
$ kcf tfwsa
```

Remove a workspace that we are going to select in fuzzy:

```bash
$ kcf tfwsr
```

## Installation
- **Manual:** MacOS, Linux and any POSIX environment with Bash/Zsh installed.
- **Homebrew:** soon and recommanded for MacOs.

### Manual

```bash
$ sudo git clone https://github.com/uthng/kcf.git /opt/kcf
$ chmod +x /opt/kcf/kcf
$ sudo ln -s /opt/kcf/kcf /usr/local/bin/kcf
```

### Homebrew

Comming soon.

## How does it work ?

- For each command, `kcf` first gets a list of resources corresponding to the given type (pods, deployment, pvc, etc) in the current namespace, in all namespaces or maybe with filters used commonly in `kubectl get`
- Then, `kcf` passes it to `Fzf` to allow you to select one or multiple values depending on command type.
- Once a value selected, `kcf` performs the given command with common `kubectl` options, if specified.
- If a container needs to be specified, `kcf` does another `get` on the related pod to retrieve the container list and passes it to `Fzf`.
- If the command is not supported (neither be in kubectl command list nor in custom one), `kubectl` will be used as fallback.
- It works in the same way for `kustomize` or `terraform`.

***Notes:*** Not all but `kcf`can be used with almost common commands, resource types, filters and options as you usually do with `kubectl`. The only thing you must respect is the syntax:
```
kcf <CMD> <TYPE> <OPTION1>...<OPTIONX> -- <ARG1> ... <ARGX>
```
