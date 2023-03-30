# Google Cloud VPS üzerinde Taiko Node kurulumu

### Donanım Gereksinimleri

#### Minimum:

- 2+ çekirdekli CPU
- 4GB RAM
- Mainnet'i senkronize etmek için 1 TB boş depolama alanı
- 8 MBit/sn indirme İnternet hizmeti

#### Önerilen:

- 4+ çekirdekli hızlı CPU
- 16GB+ RAM
- En az 1 TB boş alana sahip yüksek performanslı SSD
- 25+ MBit/sn indirme İnternet hizmeti

### Ön Gereksinimler

- Docker yüklü ve çalışıyor olmalıdır.
- Git yüklü olmalıdır.
- 1TB boş alan haricinde Geth minimum donanım gereksinimlerine bakın (çok fazla ihtiyacınız olmayacak, ~50GB bile yeterli olacaktır).

### Kurulum Adımları

1. Ubuntu 20.04 işletim sistemine sahip bir Google Cloud Compute Engine örneği oluşturun. Tavsiye edilen donanım gereksinimlerini karşılayan bir makine türünü seçebilirsiniz.

2. SSH kullanarak bağlanın.

3. Gerekli paketleri yükleyin:

   ```bash
   sudo apt-get güncellemesi
   sudo apt-get install git docker.io
   ```

4. Taiko Node repositorysini klonlayın:

   ```bash
   git clone https://github.com/taikoxyz/simple-taiko-node.git
   cd simple-taiko-node
   ```

5. Node'unuzu yapılandırın (gerekli)

- İlk olarak, .env.sample dosyasını yeni bir .env dosyasına kopyalayın:

  ```bash
    cp .env.sample .env
  ```

- Ardından, .env dosyasını tercih ettiğiniz metin düzenleyicisinde açın:

  ```bash
    nano .env
  ```

- Son olarak, aşağıdaki ortam değişkenlerini ayarlayın:

  ```bash
  L1_ENDPOINT_HTTP=https://eth-mainnet.alchemyapi.io/v2/<YOUR-ALCHEMY-API-KEY>
  L1_ENDPOINT_WS=wss://eth-mainnet.ws.alchemyapi.io/v2/<YOUR-ALCHEMY-API-KEY>
  ```

  Birkaç yerden Sepolia L1 uç noktası alabilirsiniz, Alchemy ve Infura iki popüler RPC sağlayıcısıdır. RPC'yi Ethereum mainnet olarak değil Sepolia testnet olarak seçtiğinizden emin olun.

6. Node'unuzu bir prover olarak etkinleştirin (bir prover çalıştırmak istiyorsanız isteğe bağlıdır. Ben bu adımı yapmadım)

- Daha fazla bilgi için [bir prover etkinleştirme](https://taiko.xyz/docs/guides/enable-a-prover)'ye bakın.

7. Node'u başlatın

- Eğer alpha-1 testnet node'u çalıştırdıysanız, önce eski volumleri kaldırmak için `docker-compose down -v` komutunu çalıştırmanız gerekir.

- Ayrıca, genesis bloğundan senkronize etmek biraz zaman alabilir. Bu ilerlemeyi loglar ile veya yerel grafana  kontrol panelinde izleyebilirsiniz. Güncel L2 block durumunu [Taiko alpha-2 block explorer](https://explorer.a2.taiko.xyz) görebilirsiniz.

- Docker'ın çalıştığından emin olun ve ardından node'u başlatmak için aşağıdaki komutu çalıştırın. Eğer arka planda çalıştırmak istiyorsanız, lütfen `-d` flag'ını ekleyin (`docker-compose up -d`).

  ```bash
  sudo docker-compose yukarı
  ```

- Node'u arka planda çalıştırmak için (`detached mode`) `-d` flag'ını kullanın:

  ```bash
  sudo docker-compose up -d
  ```

8. İlerlemeyi kontrol etmek ve durum panosunu görüntülemek için logları izleyin (isteğe bağlı)

- Aşağıdaki komutu terminalinizde çalıştırarak düğüm senkronizasyonunun ilerlemesini kontrol etmek için günlükleri izleyebilirsiniz:

  ```bash
  sudo docker-compose logs -f
  ```

- Ek olarak, Prometheus veri kaynağına sahip bir Grafana kontrol paneli, L2 yürütme motorunun gerçek zamanlı durumunu göstermek için kullanılabilir. SSH tünelleme kullanarak bağlantı noktasını yönlendirerek paneli görüntüleyebilirsiniz. Önce, aşağıdaki komutu çalıştırın:

    ```bash
    sudo apt-get install -y socat
    socat TCP-LISTEN:3000,fork TCP:localhost:3000
    ```

  Web tarayıcınızda `http://<your-instance-external-ip>:3000` adresine giderek gösterge tablosuna erişin.

  Ardından, web tarayıcınızı açın ve `http://<your-instance-external-ip>:3000/d/L2ExecutionEngine/l2-execution-engine-overview?orgId=1&refresh=10s` adresine gidin. Bu, L2 yürütme motorunun gerçek zamanlı durumunu içeren Grafana panosunu görüntüleyecektir.

10. Node'u durdurmak için

```bash
sudo docker-compose aşağı
```

11. Node'u kaldırmak için

```bash
sudo docker compose down -v
sudo rm -f .env
```

12. Node'u güncelleyin için

```bash
sudo docker compose pull
```


### Kaynaklar

- https://taiko.xyz/docs/guides/run-a-node
- https://github.com/ethereum/go-ethereum#hardware-requirements
