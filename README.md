# Stellar Appliance CLI

A command-line operations tool for KVM hosts running Stellar Cyber OpenXDR Data Processor, Sensor, or AIO components.

Stellar Appliance CLI provides one `aella_cli` interface for common day-2 operations after deployment with the OpenXDR KVM Installer.

## Scope

The CLI currently manages:

- ifupdown-based network configuration
- DNS on the management interface
- **NTPsec** configuration
- staged iptables ACL policy
- hostname, timezone, time, routes, and system information
- service operations
- VM console and autostart
- appliance monitoring and patch operations

> The current NTP backend is **NTPsec only**. Chrony, systemd-timesyncd, and legacy ntp are not supported by the current implementation.

The CLI is an operations layer. It does not replace the OpenXDR KVM Installer for initial deployment or storage/topology construction.

## Requirements

- Python 3.10+
- Linux KVM host
- sudo/root privileges for system-changing commands
- OpenXDR KVM Installer environment recommended
- NTPsec installed for NTP commands

## Installation

```bash
git clone https://github.com/xdr-labs/Stellar-appliance-cli.git
cd Stellar-appliance-cli

python3 -m venv venv
source venv/bin/activate
pip install -e .
```

Verify:

```bash
which aella_cli
```

Start:

```bash
aella_cli
```

The interactive prompt is:

```text
Welcome to Starlight Appliance
Appliance>
```

## Command model

```text
<command> <subcommand> [parameters]
```

Main command families:

| Command | Purpose |
|---|---|
| `show` | Display current state |
| `set` | Configure supported settings |
| `unset` | Remove supported settings |
| `start` | Start a service |
| `restart` | Restart a service |
| `shutdown` | Shut down a system or service |
| `console` | Open a VM console |
| `monitor` | Monitor VM/system state |
| `help` | Display command help |
| `quit` | Exit |

Use contextual help:

```text
help <command>
show <item> ?
set <item> ?
```

Tab completion is available for supported commands, interfaces, VM names, and other contextual values.

## Network

Common commands:

```text
show interface
show dns
show gateway
show route

set interface <iface> ip <IP/Mask> [gateway <IP>]
set interface <iface> gateway <IP>
set interface mgt dns <dns1> [dns2 ...]
set interface <iface> restart

unset interface <iface> <ip|gateway|restart>
```

DNS configuration is supported on the `mgt` interface.

Network changes require an interface restart to take effect:

```text
set interface <iface> restart
```

The CLI operates on the ifupdown configuration used by the installer:

```text
/etc/network/interfaces
/etc/network/interfaces.d/*.cfg
```

## NTPsec

The current implementation supports one backend:

```text
/etc/ntpsec/ntp.conf
systemd service: ntpsec
query tool: ntpq
```

Commands:

```text
show ntp
set ntp <server>
set ntp add <server> [server ...]
set ntp replace <server> [server ...]
unset ntp <server>
```

The CLI updates only its managed section, applies the NTPsec configuration, and verifies the NTPsec runtime path.

If NTPsec or `/etc/ntpsec/ntp.conf` is unavailable, NTP configuration is rejected rather than silently switching to another time service.

## ACL

Typical workflow:

```text
set acl policy
set acl <IP/network> <port|icmp|ping|all> [description]
set acl apply
show acl
```

Rules are staged before application. Use `set acl apply --reset` only when intentionally rebuilding the managed ACL state.

Policy mode determines rule action:

- whitelist → matching managed rules allow
- blacklist → matching managed rules deny

Local interface addresses remain protected by the CLI's local-host handling.

## System and VM operations

Examples:

```text
show version
show hostname
show service
show timezone
show time
show route

set timezone <timezone>
set time <YYYY-MM-DD HH:MM:SS>
set hostname <hostname>
set password

console <vm>
show autostart
set autostart <vm> [enable|disable]
monitor
show patch_history
set patches <patch_file>
```

Available VM names depend on the deployed appliance topology.

## Operational notes

- Use console or out-of-band access before making remote network changes that could cut connectivity.
- Network configuration follows the installer-managed ifupdown model.
- NTP commands require NTPsec.
- ACL changes are staged and must be explicitly applied.
- System-changing commands generally require root/sudo privileges.

## Documentation

The maintained operator guide is published with the OpenXDR KVM Installer documentation:

**https://kvm.xdr.ooo/operations/appliance-cli**

Related projects:

- OpenXDR KVM Installer: https://github.com/xdr-labs/OpenXDR-KVM-Installer
- XDR Labs portal: https://xdr.ooo/

## License

See [LICENSE](LICENSE).

---

The README intentionally stays concise. The operator guide above is the canonical user-facing command and operations reference.
