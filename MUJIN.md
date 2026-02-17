# Mujin fork configuration

This fork adds the following environment variables for deployment tuning. These are not present in upstream Fluvio.

## `FLUVIO_SPU_MAX_PARTITION_SIZE`

- **Component**: SPU
- **Default**: `107374182400` (100 GB, upstream default)
- **Added in**: [v0.18.5](https://github.com/mujin/fluvio/commit/07bb10ccc439c16e652eed79ade2a7138b13747c)

Sets the default maximum size (in bytes) for each partition before segment cleanup kicks in. The upstream default of 100 GB is impractical on smaller PVCs (10-20 Gi) because the disk fills before any per-partition limit activates.

Individual topics can override this via `storage.maxPartitionSize` in the topic spec.

## `FLUVIO_SC_HEALTH_TIMEOUT_SECS`

- **Component**: SC (Streaming Controller)
- **Default**: `90` (seconds)
- **Added in**: [v0.18.4](https://github.com/mujin/fluvio/commit/232dd2f80e464092ad337b1aa89b09fdc517e472)

Sets the heartbeat timeout for SPU health checks. If the SC receives no status message from an SPU within this window, it marks the SPU as unhealthy and disconnects it. On slow-disk environments (Azure managed-csi, Longhorn), SPUs can take 2-3 minutes to validate segments during startup, blocking heartbeats and causing a reconnect loop. Increase this value for deployments with slow storage.
