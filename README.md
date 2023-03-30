# Setting up Taiko Node on Google Cloud VPS

### Hardware Requirements

#### Minimum:

- CPU with 2+ cores
- 4GB RAM
- 1TB free storage space to sync the Mainnet
- 8 MBit/sec download Internet service

#### Recommended:

- Fast CPU with 4+ cores
- 16GB+ RAM
- High-performance SSD with at least 1TB of free space
- 25+ MBit/sec download Internet service

### Prerequisites

- Docker is installed and running.
- Git is installed.
- Consult the Geth minimum hardware requirements, with the exception of 1TB of free space (you won't need much, even ~50GB should suffice).

### Installation Steps

1. Create a Google Cloud Compute Engine instance with Ubuntu 20.04 as the operating system. You can choose the machine type that meets the recommended hardware requirements for a Taiko node.

2. Connect to your instance using SSH.

3. Install the required packages:

   ```bash
   sudo apt-get update
   sudo apt-get install git docker.io
   ```

4. Clone the Taiko Node repository:

   ```bash
   git clone https://github.com/taikoxyz/simple-taiko-node.git
   cd simple-taiko-node
   ```

5. Configure your node (required)

- First, copy the .env.sample to a new file .env:

  ```bash
    cp .env.sample .env
  ```

- Next, open the .env file in your preferred text editor:

  ```bash
    nano .env
  ```

- Finally, set the following environment variables:

  ```bash
  L1_ENDPOINT_HTTP=https://eth-mainnet.alchemyapi.io/v2/<YOUR-ALCHEMY-API-KEY>
  L1_ENDPOINT_WS=wss://eth-mainnet.ws.alchemyapi.io/v2/<YOUR-ALCHEMY-API-KEY>
  ```

  You can get a Sepolia L1 endpoint from a few places, Alchemy and Infura are two popular RPC providers. Make sure you select the RPC as Sepolia testnet, and not Ethereum mainnet.

6. Enable your node as a prover (optional if you want to run a prover. I didn't do this step)

- See [enable a prover](https://taiko.xyz/docs/guides/enable-a-prover) for more information.

7. Start a node

- If you ran an alpha-1 testnet node make sure to first run a `docker-compose down -v` to remove the old volumes.

- Also, it may take some time to synchronize from the genesis block. You can monitor this progress through logs, or in the local grafana dashboard and see the latest L2 chain status in the [Taiko alpha-2 block explorer](https://explorer.a2.taiko.xyz/).

- Make sure Docker is running and then run the following command to start the node. If you want to run it in the background, please add the `-d` flag (`docker-compose up -d`).

  ```bash
  sudo docker-compose up
  ```

- To run the node in the background (detached mode), use the `-d` flag:

  ```bash
  sudo docker-compose up -d
  ```

8. Monitor the logs to check the progress and view the status dashboard (optional)

- You can monitor the logs to check the progress of the node synchronization by running the following command in your terminal:

  ```bash
  sudo docker-compose logs -f
  ```

- Additionally, a Grafana dashboard with a Prometheus datasource is included to display the L2 execution engine's real-time status. You can view the dashboard by forwarding the port using SSH tunneling. First, run the following command:

    ```bash
    sudo apt-get install -y socat
    socat TCP-LISTEN:3000,fork TCP:localhost:3000
    ```

  Access the dashboard by navigating to `http://<your-instance-external-ip>:3000` in your web browser.

  Then, open your web browser and go to `http://<your-instance-external-ip>:3000/d/L2ExecutionEngine/l2-execution-engine-overview?orgId=1&refresh=10s`. This will display the Grafana dashboard with the L2 execution engine's real-time status.

10. Stop the node

```bash
sudo docker-compose down
```

11. Remove a node

```bash
sudo docker compose down -v
sudo rm -f .env
```

12. Update a node

```bash
sudo docker compose pull
```


### Sources

- https://taiko.xyz/docs/guides/run-a-node
- https://github.com/ethereum/go-ethereum#hardware-requirements
