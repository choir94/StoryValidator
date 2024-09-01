# StoryValidator
<h2 align=center> Story Protocol Validator Node </h2>

# Vps Configuration

| Component  | Requirement     |
|------------|-----------------|
| **CPU**    | 4 Cores         |
| **RAM**    | 8 GB            |
| **Disk**   | 200 GB          |
| **Bandwidth** | 10 MBit/s    |

# Installation
- Pertama buat 2 direktori menggunakan perintah di bawah ini
```bash
mkdir -p ~/.story/geth ~/.story/story
```
- Install dependencies
```bash
sudo apt install screen curl git make jq build-essential gcc unzip wget lz4 aria2 -y
```
- Download and extract `geth` File Binary di `~/.story/geth` directory
```bash
cd ~/.story/geth && wget https://story-geth-binaries.s3.us-west-1.amazonaws.com/geth-public/geth-linux-amd64-0.9.2-ea9f0d2.tar.gz && tar -xvf geth-linux-amd64-0.9.2-ea9f0d2.tar.gz
```
- buat Screen `geth`
```bash
screen -S geth
```
- Start `geth`
```bash
geth-linux-amd64-0.9.2-ea9f0d2/geth --iliad --syncmode full
```
- Keluar screen `Ctrl+A+D` Dari `geth`
- Ke `Home` directory
```bash
cd
```
- Download and extract `story` File Binary di `~/.story/story` directory
```bash
cd ~/.story/story && wget https://story-geth-binaries.s3.us-west-1.amazonaws.com/story-public/story-linux-amd64-0.9.11-2a25df1.tar.gz && tar -xvf story-linux-amd64-0.9.11-2a25df1.tar.gz
```
- Buat Screen Lagi `story`
```bash
screen -S story
```
- Instal Dan jalankan `story` Pakai ini
```bash
story-linux-amd64-0.9.11-2a25df1/story init --network iliad --force
```
```bash
story-linux-amd64-0.9.11-2a25df1/story run
```
- Setelah menjalankan perintah ini, Anda mungkin menghadapi masalah, jadi untuk mengatasinya gunakan perintah di bawah ini
- Pertama Stop gunakan `Ctrl+C`
- Edit file `config.toml`
```bash
nano config/config.toml
```
- Cari Bagian `P2P configuration section`, Hapus File yang di awali `persistent_peers=""`, Kemudian Ganti dengan ini, copy paste
```bash
persistent_peers= "f16c644a6d19798e482edcfe5bd5728a22aa5e0d@65.108.103.184:26656,9fc21eaa5f39f3611875a951775c5b1ebdf032ee@84.32.186.154:26656,a320f8a15892bddd7b5502527e0d11c5b5b9d0e3@69.67.150.107:29931,537b4c11a17f282bd9f84ba578e5998944c49c79@176.9.155.156:28656"
```
- Simpan Klik `Ctrl+X` Kemudian `Y` Dan `Enter`
- Jika Anda memeriksanya sekarang, Anda akan melihat perbedaan yang signifikan antara node Anda dan blok terbaru. Node Anda perlu melakukan sinkronisasi dengan blok terbaru. 
```bash
echo $(( $(curl -s https://staking.testnet.storyrpc.io/api/network_status | jq -r '.msg.consensus_block_height') - $(curl -s localhost:26657/status | jq -r '.result.sync_info.latest_block_height') ))
```
- Jadi, saya akan merekomendasikan Anda untuk menggunakan snapshot, itu tidak berarti bahwa node Anda akan segera disinkronkan dengan blok terbaru
- Pertama Stop `story` Pakai `Ctrl+C` Kemudian keluar screen `Ctrl+A+D`
- ke `home` directory 
```bash
cd
```
- Reattach with `geth` screen session now, and then use `Ctrl+C` to stop it and then detach from this screen session using `Ctrl+A+D`
- Now visit [this docs](https://service.josephtran.xyz/testnet/story/snapshot)  by **Joseph Tran**, You don't need to stop node using his command, as our command is different, you just do these things from that doc `Download Geth-data and Story-data` , `Backup priv_validator_state.json` , `Remove old data` , `Extract Story-data and geth-data` , `Move priv_validator_state.json back`
- Now come back to my guide and use this command to restart again
```bash
screen -r geth
```
- Now use this command to start `geth` again
```bash
geth-linux-amd64-0.9.2-ea9f0d2/geth --iliad --syncmode full
```
- Detach from this screen session again using `Ctrl+A+D`
- Use the below command to reattach to `story` screen session
```bash
screen -r story
```
- Use this command to start `story`
```bash
story-linux-amd64-0.9.11-2a25df1/story run
```
- Detach from this screen session using `Ctrl+A+D`
- You need to wait until your node sync with latest block, you can check how many blocks are still left to synchronised with your node using below command
```bash
echo $(( $(curl -s https://staking.testnet.storyrpc.io/api/network_status | jq -r '.msg.consensus_block_height') - $(curl -s localhost:26657/status | jq -r '.result.sync_info.latest_block_height') ))
```
![image](https://github.com/user-attachments/assets/e731eef9-427e-4364-a7a6-ce42bca1cc25)

- If you see block number is reducing in each checking, you are good
- You can also simply use the below command to check `catching_up` is `false`
```bash
curl -s localhost:26657/status | jq
```
- You need to wait until you see `catching_up` is `false`
- You can close your terminal now and check the status after 3-5 hrs using above 2 commands
- By default, when you initialised `story` in above steps,  a `validator key` is created for you. To view your validator key, run the following command:
```bash
$HOME/.story/story/story-linux-amd64-0.9.11-2a25df1/story validator export
```
- Request some [faucet](https://faucet.story.foundation/) (atleast 3 $IP) to this `EVM Public Key`
- In addition, if you want to export the derived EVM private key of your validator into the default data config directory, please run the following:
```bash
$HOME/.story/story/story-linux-amd64-0.9.11-2a25df1/story validator export --export-evm-key
```
- View your this EVM wallet private key and save it somewhere
```bash
cat $HOME/.story/story/config/private_key.txt
```
- Now create a validator using this command
```bash
story validator create --stake 1000000000000000000 --private-key PRIVATE_KEY_OF_EVM_YOU_COPIED_IN_THE_AVOBE_STEP
```
- Now copy this validator wallet's pruvate key as well and save it
```bash
cat $HOME/.story/story/config/priv_validator_key.json
```
- To participate in consensus, at least 1024 IP must be staked (equivalent to 1000000000000000000 wei) as of now lol
```bash
story validator stake \
   --validator-pubkey "VALIDATOR_PUB_KEY_IN_BASE64" \
   --stake 1024000000000000000000 \
   --private-key PRIVATE_KEY_OF_EVM_YOU_COPIED_EARLIER
```
- Replace `VALIDATOR_PUB_KEY_IN_BASE64` with the value you will get after running this command
```bash
curl -s localhost:26657/status | jq -r '.result.validator_info.pub_key.value'
```
- Check your validator on [Explorer](https://testnet.story.explorers.guru/)
