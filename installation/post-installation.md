# Post-Installation Steps

## Configure Firewall

* Allow remote access to the ATSD network services by [adding `iptables` rules](firewall.md).

## Setup Email Client

* See [note](../administration/setting-up-email-client.md).

## Verify System Time

* Open the **Admin > Server Time** tab in the ATSD web interface and verify the
time and timezone information.
* Synchronize system time or setup NTP in order to keep the server time accurate.

![Server\_time](images/Server_time.png)

## Increase Network Buffers

> If you're anticipating insertion rate with bursts of 100K+ packets per second, 
increase maximum receiving buffer.

* See [note](../administration/networking-settings.md).

## Enable Swap Memory

* See [note](../administration/enabling-swap-space.md).

## Increase Java Memory Allocations

* See [note](../administration/allocating-memory.md).

## Review Log Files

* See [note](../administration/logging.md).
