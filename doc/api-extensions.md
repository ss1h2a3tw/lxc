# API extensions

The changes below were introduced to the LXC API after the 3.0 API was finalized.

They are all backward compatible and can be detected by client tools by
called the `lxc_has_api_extension` function.

## lxc\_log

This introduces a way to initialize a logging instance from the API for a given
container.

## lxc\_config\_item\_is\_supported

This introduces the `lxc_config_item_is_supported` function. It allows users to
check whether their LXC instance supports a given configuration key.

## console\_log

This adds support to container's console log. The console log is implemented as
an efficient ringbuffer.

## reboot2

This adds `reboot2()` as a new API extension. This function properly waits
until a reboot succeeded. It takes a timeout argument. When set to `> 0`
`reboot2()` will block until the timeout is reached, if timeout is set to zero
`reboot2()` will not block, if set to -1 `reboot2()` will block indefinitely.

## mount\_injection

This adds support for injecting and removing mounts into/from a running
containers. Two new API functions `mount()` and `umount()` are added. They
mirror the current mount and umount API of the kernel.

## seccomp\_allow\_nesting

This adds support for seccomp filters to be stacked regardless of whether a seccomp profile is
already loaded. This allows nested containers to load their own seccomp profile.

## seccomp\_notify

This adds "notify" as seccomp action that will cause LXC to register a seccomp listener and retrieve
a listener file descriptor from the kernel. When a syscall is made that is registered as "notify"
the kernel will generate a poll event and send a message over the file descriptor.

The caller can read this message, inspect the syscalls including its arguments. Based on this information the caller is expected to send back a message informing the kernel which action to take. Until that message is sent the kernel will block the calling process. The format of the messages to read and sent is documented in seccomp itself.

## network\_veth\_routes

This introduces the `lxc.net.[i].veth.ipv4.route` and `lxc.net.[i].veth.ipv6.route` properties
on `veth` type network interfaces. This allows adding static routes on host to the container's
network interface.

## network\_ipvlan

This introduces the `ipvlan` network type.

Example usage:

```
lxc.net[i].type=ipvlan
lxc.net[i].ipvlan.mode=[l3|l3s|l2] (defaults to l3)
lxc.net[i].ipvlan.isolation=[bridge|private|vepa] (defaults to bridge)
lxc.net[i].link=eth0
lxc.net[i].flags=up
```

## network\_l2proxy

This introduces the `lxc.net.[i].l2proxy` that can be either `0` or `1`. Defaults to `0`.
This, when used with `lxc.net.[i].link`, will add IP neighbour proxy entries on the linked device
for any IPv4 and IPv6 addresses on the container's network device.

For IPv4 addresses it will check the following sysctl values and fail with an error if not set:

```
net.ipv4.conf.[link].forwarding=1
```

For IPv6 addresses it will check the following sysctl values and fail with an error if not set:

```
net.ipv6.conf.[link].proxy_ndp=1
net.ipv6.conf.[link].forwarding=1
```

## network\_gateway\_device\_route

This introduces the ability to specify `lxc.net.[i].ipv4.gateway` and/or
`lxc.net.[i].ipv6.gateway` with a value of `dev` which will cause the default gateway
inside the container to be created as a device route without destination gateway IP needed.
This is primarily intended for use with layer 3 networking devices, such as IPVLAN.

## network\_phys\_macvlan\_mtu

This introduces the ability to specify a custom MTU for `phys` and `macvlan` devices using the
`lxc.net.[i].mtu` property.
