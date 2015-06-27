# Spotty

This is a pretty loose collection of scripts to run Minecraft servers on AWS spot instances. Makes a bunch of assumptions about my specific environment and preferences.

## Local Machine set up
```
aws ec2 import-key-pair --public-key-material file://~/.ssh/id_rsa.pub --key-name dave
```

## Configure security groups
```
aws ec2 create-security-group --group-name ssh-standard --description 'SSH on default port'
aws ec2 authorize-security-group-ingress --group-name ssh-standard --protocol tcp --port 22 --cidr '0.0.0.0/0'

aws ec2 create-security-group --group-name minecraft --description 'Minecraft'
aws ec2 authorize-security-group-ingress --group-name minecraft --protocol tcp --port 25565 --cidr '0.0.0.0/0'
aws ec2 describe-security-groups
```

## Request instance
```
./request-spot
./spot-requests
./cluster
```

## IP
```
aws ec2 allocate-address
aws ec2 associate-address --instance-id $INSTANCE_ID --public-ip $IP
```

## Create new volume
```
aws ec2 create-volume --size 10 --volume-type gp2 --availability-zone us-west-1a
# sudo mkfs -t ext4 /dev/xvdf
```

## Create volume from snapshot
```
aws ec2 create-volume --size 1 --volume-type gp2 --availability-zone us-west-1a --snapshot-id $MF_SNAP_ID
```

## Attach volume
```
./cluster
./volumes
./attach-volume vol-bf1ddb46 i-3d99e2ff
```

## Set DNS
```
./set-dns $IP
```

## Create snapshot
```
aws ec2 create-snapshot --volume-id $MF_VOLUME_ID
aws ec2 describe-snapshots --filter "Name=volume-id,Values=$MF_VOLUME_ID"
```

## Clean up
aws ec2 terminate-instances --instance-ids $MF_INSTANCE_ID

## Upgrading
```
docker run -it --rm -v /mnt/data:/data --entrypoint /bin/bash partycloud/minecraft:ftb-infinity
mv mods old_mods
cp -R /srv/mods mods
# Ctrl^D
docker start -i minecraft
```
