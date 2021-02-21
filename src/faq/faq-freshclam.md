# Freshclam FAQ

The following FAQ should help you understand why `freshclam` may have failed to fetch the latest updates.

## Invalid DNS reply. Falling back to HTTP mode or ERROR: Can't query current.cvd.clamav.net

There is a problem with your DNS server. Please check the entries in `/etc/resolv.conf` and verify that you can resolve the `TXT` record manually:

`$ host -t txt current.cvd.clamav.net`

If you can't, it means your network is broken. You'll be still able to download the updates, but you'll waste a lot of bandwidth checking for updates. Please note that some not RFC compliant DNS servers (namely the one shipped with the *Alcatel* (now *Thomson*) *SpeedTouch 510 modem*) can't resolve `TXT` record. If that's the case, please recompile ClamAV with the flag `--enable-dns-fix`.

## ERROR: Connection with ??? failed

Either your dns servers are not working or you are blocking `port 53/tcp`. You should manually check that you can resolve hostnames with:

`$ host database.clamav.net`

If it doesn't work, check your dns settings in `/etc/resolv.conf`. If it works, check that you can receive dns answers longer than 512 bytes, e.g. check that your firewall is not blocking packets which originate from `port 53/tcp`. An easy way to find it out is:

`$ dig @ns1.clamav.net db.us.big.clamav.net`

## WARNING: Incremental update failed, trying to download daily.cvd

For some reason, incremental update failed. Freshclam can recover from this situation by downloading the whole daily.cvd.

## Database update process failed: Downloaded database had lower version than advertised

For some reason, the content delivery network is not serving the latest updates yet. If you experience this problem, please report the issue on [Bugzilla](https://bugzilla.clamav.net/buglist.cgi?component=Mirror&product=Mirror%20Issues).

## Update failed. Your network may be down or the ClamAV database content delivery network is experiencing an outage

It's not your lucky day. Your network may be down or the ClamAV database content delivery network is experiencing an outage. Please wait a few minutes and try again. Remember to pass the `-v` option to freshclam.

## Update failed. Updating too frequently with an outdated version

Starting from ClamAV 0.9x, whenever your ClamAV engine becomes outdated and the difference between the functionality level required by the CVD and the functionality level supported by your ClamAV engine is more than 3, freshclam will refuse to check for updates more often than 6 times per day.

The reason for this is that bandwidth can be expensive. It is not helpful to generate extra traffic on our content delivery network if you cannot take advantage of all the signatures anyway. If you really care about catching as much malware as possible and you want to check for updates more often than 6 times per day, then you should also not run such an old version of ClamAV.

## Your ClamAV installation is OUTDATED

This message does NOT indicate that you are unable to download the latest CVD update! You'll get this message whenever a new version of ClamAV is released. In order to detect all the latest viruses, it's not enough to keep your database up to date. You also need to run the latest version of the scanner.

## WARNING: Current functionality level = 1, required = 2

The functionality level of the database determines which scanner engine version is required to use all of its signatures. If you don't upgrade immediately you will still be able to download the latest CVD updates but the engine won't be able to use ALL of them.

## Ignoring mirror &lt;IP&gt; (has connected too many times with an outdated version)

If you are experiencing this problem, please do the following:  Stop the `freshclam` daemon if it's running, delete both `mirrors.dat` and `daily.cvd`, then restart the `freshclam` daemon. Freshclam will then download a new `daily.cvd` and will be up-to-date.

## For all other database update related failures

Please report freshclam update failures or issues on [Bugzilla](https://bugzilla.clamav.net/buglist.cgi?component=Mirror&product=Mirror%20Issues).