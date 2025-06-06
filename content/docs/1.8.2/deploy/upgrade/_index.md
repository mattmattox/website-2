---
title: Upgrade
weight: 3
---

Here we cover how to upgrade to the latest Longhorn from all previous releases.

# Deprecation & Incompatibility

There are no deprecated or incompatible changes introduced in v{{< current-version >}}.

# Upgrade Path Enforcement and Downgrade Prevention

Starting with v1.5.0, Longhorn only supports upgrades from one minor version to the next. For example, upgrading from 1.5.x to 1.6.x is supported, but skipping versions (e.g., from 1.4.x to 1.6.x) is not. If you attempt to upgrade from an unsupported version or skip a minor version, the operation will fail automatically. However, you can revert to the previously installed version without service interruption or downtime.

Moreover, Longhorn does not support downgrades to earlier versions. This restriction helps prevent unexpected system behavior and issues associated with function incompatibility, deprecation, or removal.

> **Warning**:
> - Once you successfully upgrade to v{{< current-version >}}, you will not be allowed to revert to the previously installed version.
> - The Downgrade Prevention feature was introduced in v1.5.0 so Longhorn is unable to prevent downgrade attempts in older versions.
However, downgrading is completely unsupported and is therefore not recommended.

The following table outlines the supported upgrade paths.

  |  Current version |  Target version |  Supported | Example |
  |    :-:      |    :-:      |   :-:  |    :-:    |
  |  x.y.*      |  x.(y+1).*  |   ✓    |  v1.4.2  to  v1.5.1  |
  |  x.y.*      |  x.y.(*+n)  |   ✓    |  v1.5.0  to  v1.5.1  |
  |  x.y[^lastMinorVersion].*      |  (x+1).y.*  |   ✓    |  v1.30.0 to  v2.0.0  |
  |  x.(y-1).*  |  x.(y+1).*  |   X    |  v1.3.3  to  v1.5.1  |
  |  x.(y-2).*  |  x.(y+1).*  |   X    |  v1.2.6  to  v1.5.1  |

[^lastMinorVersion]: Longhorn only allows upgrades from any patch version of the last minor release before the new major version. For example, if v1.3.0 is the last minor version before v2.0, you can upgrade from any patch version of v1.3.0 to any patch version of v2.0.

## Manual Checks Before Upgrade
Automated checks are only performed on some upgrade paths, and the pre-upgrade checker may not cover some scenarios.  Manual checks, performed using either kubectl or the UI, are recommended for these schenarios.  You can take mitigating actions or defer the upgrade until issues are addressed.
- Ensure that all V2 Data Engine volumes are detached and the replicas are stopped.  The V2 Data Engine currently does not support live upgrades.
- Avoid upgrading when volumes are in the "Faulted" status.  If all the replicas are deemed unusable, they may be deleted and data may be permanently lost (if no usable backups exist).
- Avoid upgrading if a failed BackingImage exists.  For more information, see [Backing Image](../../advanced-resources/backing-image/backing-image).
- It is recommended to create a [Longhorn system backup](../../advanced-resources/system-backup-restore/backup-longhorn-system) before performing the upgrade. This ensures that all critical resources, such as volumes and backing images, are backed up and can be restored in case any issues arise.

# Upgrading Longhorn

There are normally two steps in the upgrade process: first upgrade Longhorn manager to the latest version, then manually upgrade the Longhorn engine to the latest version using the latest Longhorn manager.

## 1. Upgrade Longhorn manager

- To upgrade from v1.7.x, see [this section.](./longhorn-manager)

## 2. Manually Upgrade Longhorn Engine

After Longhorn Manager is upgraded, Longhorn Engine also needs to be upgraded [using the Longhorn UI.](./upgrade-engine)

## 3. Automatically Upgrade Longhorn Engine

Since Longhorn v1.1.1, we provide an option to help you [automatically upgrade engines](./auto-upgrade-engine)

## 4. Automatically Migrate Recurring Jobs

With the introduction of the new label-driven `Recurring Job` feature, Longhorn has removed the `RecurringJobs` field in the Volume Spec and planned to deprecate `RecurringJobs` in the StorageClass.

During the upgrade, Longhorn will automatically:
- Create new recurring job CRs from the `recurringJobs` field in Volume Spec and convert them to the volume labels.
- Create new recurring job CRs from the `recurringJobs` in the StorageClass and convert them to the new `recurringJobSelector` parameter.

Visit [Recurring Snapshots and Backups](../../snapshots-and-backups/scheduling-backups-and-snapshots) for more information about the new `Recurring Job` feature.

# Extended Reading

Visit [Some old instance manager pods are still running after upgrade](https://longhorn.io/kb/troubleshooting-some-old-instance-manager-pods-are-still-running-after-upgrade) for more information about the cleanup strategy of instance manager pods during upgrade.

# Need Help?

If you have any issues, please report it at
https://github.com/longhorn/longhorn/issues and include your backup yaml files
as well as manager logs.
