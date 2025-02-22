---
description: A basic instruction about how to set up self-hosting
---

# Self-hosting

We believe that any software should support fundamental digital freedoms. With the rise of cryptography and computer systems, it is now possible to guarantee these freedoms in the world of bits: privacy of thoughts, freedom of speech, right to authorship, and autonomy from software providers. These rights can be encoded into the code, which when open, can be freely verified by anyone. This way, trust among users and developers can be established.

This is our way. By opening our source code, we ensure that our users have complete autonomy and independence from the Any Association. They retain the ability to analyze, compile, and run each software component on their personal machines without relying on external parties. This guarantees uninterrupted access to the tools and data they generate and store, shielding them from any potential restrictions.

This article will help you to self-host Any-Sync on your own infrastructure for personal use and configure Anytype clients to work with your nodes.

## Prerequisites

To ensure compatibility, please use Go version `1.19` for building `any-sync-*` and `anytype-heart`.

You will need a MongoDB to run Any-Sync Coordinator Node, and an S3-compatible object storage and Redis to run Any-Sync File Node.

One of the possible S3-compatible solutions is [MinIO](https://min.io/docs/minio/linux/operations/install-deploy-manage/deploy-minio-single-node-single-drive.html).

## Steps

1. Decide what network topology you will need for `any-sync`. A set of coordinator, sync and file nodes, one of each, will be enough for personal use.
2. Set up MongoDB, S3-compatible object storage and Redis and save their properties to display them in configuration. You will need:
   * MongoDB:
     * Connection URL
     * Database name
   * S3 Bucket:
     * Endpoint (in case you proceed with S3-compatible object storage)
     * Region name
     * Profile
     * Bucket name
     * Credentials (see [`aws-sdk-go` documentation](https://pkg.go.dev/github.com/aws/aws-sdk-go#readme-configuring-credentials))
   * Redis:
     * Connection URL
3.  Create configurations for future `any-sync-*` nodes and `anytype-heart`.\
    You can generate basic configuration for your nodes with [`any-sync-network`](https://github.com/anyproto/any-sync-tools) tool, or you can create more complex configurations on your own (please see [Any-Sync Configuration](../any-sync/configuration.md "mention") for details).

    If you decide to use `any-sync-network` tool, please run following commands and use interactive CLI to generate configuration files:

    ```bash
    git clone https://github.com/anyproto/any-sync-tools.git
    cd any-sync-tools
    go install ./any-sync-network
    any-sync-network create
    ```
4. Download and build [`any-sync-coordinator`](https://github.com/anyproto/any-sync-coordinator), [`any-sync-node`](https://github.com/anyproto/any-sync-node), and [`any-sync-filenode`](https://github.com/anyproto/any-sync-filenode) see `README.md` inside each repo for details.
5. Run nodes using proper configuration files. If you generated them with `any-sync-network` tool, use:
   * `coordinator.yml` for `any-sync-coordinator` node,
   * `sync-N.yml` for each of `any-sync-node`s,
   * `file-N.yml` for each of `any-sync-filenode`s.
6. Download [`anytype-heart`](https://github.com/anyproto/anytype-heart) middleware library. Build it for the clients you need, using your network configuration. If you generated it with `any-sync-network` tool, use `heart.yml`:

    ```bash
    # Build and install for desktop client
    make install-dev-js ANY_SYNC_NETWORK=heart.yml
    ```

    ```bash
    # Build for iOS client
    make build-ios ANY_SYNC_NETWORK=heart.yml
    ```

    ```bash
    # Build for Android client
    make build-android ANY_SYNC_NETWORK=heart.yml
    ```
    See `anytype-heart` [`README.md`](https://github.com/anyproto/anytype-heart#build-from-source) for details.
8. The last step! Build clients you need from the source. Refer to `README.md` in clients repositories for more details:
    - [`anytype-ts`](https://github.com/anyproto/anytype-ts)
    - [`anytype-kotlin`](https://github.com/anyproto/anytype-kotlin)
    - [`anytype-swift`](https://github.com/anyproto/anytype-swift)

## Conditions

`any-sync` networks are permissionless. This means that anyone who knows the IP and port of the coordinator node can connect to it, create new spaces, or download existing encrypted objects. Data inside spaces is always secure, as it requires space encryption keys to read it. Nodes do not store and do not have access to these encryption keys and cannot decrypt the data. Refer to the [Any-Sync Protocol overview](../any-sync/overview.md "mention") for additional details.

Middleware library `anytype-heart` and Anytype clients `anytype-ts`, `anytype-swift` and `anytype-kotlin` are licensed under [Any Source Available License 1.0](https://networks.any.coop).

It allows Non-Commercial use, but if you plan a Commercial use for your self-hosted instance, you will need to obtain a separate use grant from us. Please contact [license@any.coop](mailto:license@any.coop) in this case first.
