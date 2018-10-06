# Volumes

### Mounting

Mounting Docker volumes via Docker for Mac can result in performance bottlenecks as seen in [this issue](https://github.com/docker/for-mac/issues/77). This also heavily impacts the performance of my [Elixir base16 builder](https://github.com/obahareth/base16-builder-elixir/issues/2).

On macOS, you can [configure mount consistency](https://docs.docker.com/storage/bind-mounts/#configure-mount-consistency-for-macos) to get better performance on your use case. These are the three available consistencies:

* `consistent` \(the default\) - This is the slowest setting and it aims for full consistency between the container and host
* `delegated` - This setting treats the container view as the source of truth, changes in the container can take a while to appear on the host. This setting is better used when the container is making the majority of the changes \(especially if they're a lot\). In my Elixir base16 builder, this is the setting I chose because A LOT of files are written in the container that should then be reflected back to the host, and I don't really care about the host's version of the files.
* `cached` - This setting treats the host as the source of truth, changes in the host may take a while to appear in the container. This setting is better used for development where you always want the latest changes on your host to show up on the container, and you aren't really going to make changes inside of the container only.

