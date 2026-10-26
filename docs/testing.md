# Testing and Validation

This document records the tests performed on the Raspberry Pi 5 homelab project running AdGuard Home and Tailscale.

The tests focus on DNS resolution, domain blocking, private connectivity, and backup operation.

## 1. DNS Resolution on Raspberry Pi

**Test:** Resolve a normal domain through AdGuard Home.

Command:

```bash
nslookup example.com 192.168.1.4
```

**Observed result:** The query returned valid IPv4 and IPv6 addresses.

**Status:** Passed.

This confirms that AdGuard Home can resolve normal domains through the configured upstream DNS resolver.

## 2. DNS Filtering on Raspberry Pi

**Test:** Query a domain expected to be blocked by the configured blocklists.

Command:

```bash
nslookup doubleclick.net 192.168.1.4
```

**Observed result:** The response returned `0.0.0.0` and `::`.

**Status:** Passed.

This confirms that the configured filtering rules block this domain.

## 3. Tailscale Connectivity

**Test:** Check connectivity from Windows to the Raspberry Pi through Tailscale.

Command:

```cmd
tailscale ping <PI_TAILSCALE_IP>
```

**Observed result:** The Raspberry Pi responded successfully with a reported latency of approximately 7 ms during the test.

**Status:** Passed.

This confirms connectivity between the tested Windows client and the Raspberry Pi at that time.

## 4. DNS Filtering from Windows

**Test:** Query a known advertising domain while Windows is connected to Tailscale.

Command:

```powershell
nslookup doubleclick.net
```

**Observed result:** The response returned `0.0.0.0` and `::`. The DNS server displayed by Windows was the Tailscale DNS proxy.

**Status:** Passed.

This confirms that the tested Windows client received a blocked response through its Tailscale DNS configuration.

## 5. Normal DNS Resolution from Windows

**Test:** Resolve a normal domain.

Command:

```powershell
nslookup example.com
```

**Observed result:** The query returned valid IPv4 and IPv6 addresses.

**Status:** Passed.

This confirms that normal domain resolution worked during the test.

## 6. Backup Operation

The scheduled backup service was previously run and tested successfully.

The backup archive was copied to a Windows computer, and its SHA-256 checksum was verified against the original archive.

**Status:** Backup creation and checksum verification passed.

A full recovery test should be performed separately to verify that the saved configuration can be restored successfully.

## 7. Privacy-Conscious Logging

AdGuard Home was configured to hide client IP addresses in its logs and retain query history for a limited period.

**Status:** Configuration applied.

This reduces the identifying information and history retained in AdGuard Home. It does not remove records held by internet service providers or the websites being accessed.

## 8. Test Limitations

* Tests represent the observed behavior at the time they were performed.
* Successful DNS resolution does not guarantee that every application will work correctly.
* A blocked-domain response confirms the behavior for that domain, not every advertising or tracking domain.
* DNS filtering does not guarantee complete anonymity or protection from every online threat.
* A successful backup and checksum verification do not replace a full restore test.

## Conclusion

The tests confirmed normal DNS resolution, blocking of a known advertising domain, Tailscale connectivity, and successful backup verification.

The project demonstrates a working personal homelab built around Raspberry Pi 5, AdGuard Home, Tailscale, and Quad9 DNS-over-HTTPS.
