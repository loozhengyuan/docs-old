# Backup ZFS Remotely

We probably know how we can use `zfs send` and `zfs recv` to manage replication of snapshots, but doing this remotely is even more powerful. This article assumes basic knowledge of the two commands, but more information can be found on the [zfs\(8\)](https://www.freebsd.org/cgi/man.cgi?query=zfs&sektion=8) manual on FreeBSD's site. In addition, this was done both systems running NAS4Free.

## Assumptions

For the following examples, we assume: 1. Snapshots were done periodically, and; 2. The destination directory and child directory\(datasets\) were already created in `host2`.

## Step 1: Initial Replication

```text
host1# zfs send -Rv tank@latestsnapshot | ssh host2 zfs recv -Fduvs data/backup
```

This command first instructs `host1` to send a replication package and `host2` to receive it and place it in the correct directory. The following flags are used:

**zfs send**

`-R`: Generate a replication stream package, which will replicate the specified filesystem, and all descendent file systems, up to the named snapshot. When received, all properties, snap-shots, descendent file systems, and clones are preserved.

`-v`: Print verbose information about the stream package generated. This information includes a per-second report of how much data has been sent.

**zfs recv**

`-F`: Force a rollback of the file system to the most recent snap-shot before performing the receive operation. If receiving an incremental replication stream \(for example, one generated by "zfs send -R {-i \| -I}"\), destroy snapshots and file systems that do not exist on the sending side.

`-d`: Use the full sent snapshot path without the first element \(without pool name\) to determine the name of the new snapshot as described in the paragraph above.

`-u`: File system that is associated with the received stream is not mounted.

`-v`: Print verbose information about the stream and the time required to perform the receive operation.

`-s`: If the receive is interrupted, save the partially received state, rather than deleting it. Interruption may be due to premature termination of the stream \(e.g. due to network failure or failure of the remote system if the stream is being read over a network connection\), a checksum error in the stream, termination of the zfs receive process, or unclean shutdown of the system.

The receive can be resumed with a stream generated by zfs send -t token, where the token is the value of the receive\_resume\_token property of the filesystem or volume which is received into.

_Note: You can also invoke the_ `-n` _flag to do a dry run before you commit to the command._

## Step 2: Subsequent Replication

```text
host1# zfs send -Rv -I tank@earliersnapshot tank@latestsnapshot | ssh host2 zfs recv -Fduvs data/backup
```

This uses almost the same command as the previous, albeit this only copies all incremental data between `@earliersnapshot` and `@latestsnapshot`. To copy incremental data WITHOUT including the snapshots inbetween, use the small `-i` flag instead.

