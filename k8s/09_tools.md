# Tools

## [Lens](https://github.com/lensapp/lens)

**Lens** - The Kubernetes IDE ("Lens IDE") is a distribution of the OpenLens repository with Team Lens specific customizations released under a traditional EULA.

Lens IDE provides the full situational awareness for everything that runs in Kubernetes. It's lowering the barrier of entry for people just getting started and radically improving productivity for people with more experience.

Lens IDE a standalone application for MacOS, Windows and Linux operating systems. You can download it free of charge for Windows, MacOS, and Linux from Lens IDE website.

## [K9s](https://github.com/derailed/k9s)

K9s provides a terminal UI to interact with your Kubernetes clusters. The aim of this project is to make it easier to navigate, observe and manage your applications in the wild. K9s continually watches Kubernetes for changes and offers subsequent commands to interact with your observed resources.

### Installation

K9s is available on Linux, macOS and Windows platforms.

- Binaries for Linux, Windows and Mac are available as tarballs in the release page. 

    ```bash
    # Install specific version from Git
    VERSION=v0.25.18 && ORIG_FILE=k9s_Darwin_x86_64.tar.gz DEST_FILE=k9s && \
    DOWNLOAD_URL=https://github.com/derailed/k9s/releases/download/$VERSION/$ORIG_FILE && \
    wget $DOWNLOAD_URL && tar -zxvf $ORIG_FILE $DEST_FILE && chmod +x $DEST_FILE && \
    sudo mv $DEST_FILE /usr/local/bin/$DEST_FILE && rm $ORIG_FILE

    # Verify Version
    k9s version

    # Remove ~/.k9s from user folder if already exists.
    ```

- Via Homebrew for macOS or LinuxBrew for Linux

    `brew install k9s`

### Usage

```bash
# Open k9s
k9s

# Popeye -> :popeye
# Pulse -> :pulse
# Namespaces -> :ns
# Pods -> :pods
# Quit -> :quit

# Filter / cored
# Label  /-l ssd

# X-ray -> :xray pod default
```

## References

[Kubernetes Essential Tools](https://itnext.io/kubernetes-essential-tools-2021-def12e84c572)
[Running Kafka in Kubernetes](https://www.youtube.com/watch?v=aO2pv8W6oZU)
[Kubernetes Minimal Distributions](https://faun.pub/k3d-vs-k3s-vs-kind-vs-microk8s-vs-minikube-6949ebb93d18)