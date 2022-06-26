## Scaleway

### Storage Bucket

#### S3F3 Setup

```sh
sudo apt update && sudo  apt upgrade -y && \
sudo apt -y install automake autotools-dev fuse g++ git libcurl4-gnutls-dev libfuse-dev libssl-dev libxml2-dev make pkg-config
```
```sh
git clone https://github.com/s3fs-fuse/s3fs-fuse.git
```

```sh
cd s3fs-fuse
```

```sh
sed -i 's/MAX_MULTIPART_CNT         = 10 /MAX_MULTIPART_CNT         = 1 /' src/fdcache_entity.cpp
```

```sh
./autogen.sh
```

```sh
./configure
```

```sh
make
```

```sh
make install
```

```sh
cp ~/s3fs-fuse/src/s3fs /usr/local/bin/s3fs
```

#### Mounting

```sh
export ACCESS_KEY="ACCESS_KEY" && \
export SECRET_KEY="SECRET_KEY" && \
export SCW_BUCKET_NAME="SCW_BUCKET_NAME" && \
export FOLDER_TO_MOUNT="/path/to/mount" && \
export BUCKET_URL="https://s3.nl-ams.scw.cloud" && \
export ENDPOINT="nl-ams"
```

```sh
echo $ACCESS_KEY:$SECRET_KEY > $HOME/.passwd-s3fs && \
chmod 600 $HOME/.passwd-s3fs
```

```sh
s3fs $SCW_BUCKET_NAME $FOLDER_TO_MOUNT -o allow_other -o passwd_file=$HOME/.passwd-s3fs -o use_path_request_style -o endpoint=$ENDPOINT -o parallel_count=15 -o multipart_size=128 -o nocopyapi -o url=$BUCKET_URL
```

```sh
sudo nano /etc/fstab
```

```
s3fs#SCW_BUCKET_NAME /path/to/mount fuse _netdev,allow_other,use_path_request_style,url=https://s3.nl-ams.scw.cloud/ 0 0
```
