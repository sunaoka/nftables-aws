# Nftables aws script

Python script that generates a nft file with a set of AWS Ips.

## Usage

The script always generate a file for ipv4 and ipv6. You can customize some settings with arguments.

Generate set filtering by region and service.

```bash
./nft_aws.py -r eu-west-2 -s EC2 S3
```

*For check allowed values for services, regions check [this link](https://docs.aws.amazon.com/general/latest/gr/aws-ip-ranges.html#aws-ip-syntax)*

Giving the following output and two files `aws-ipv4.nft` and `aws-ipv6.nft` with the sets.

```text
Downloading json from https://ip-ranges.amazonaws.com/ip-ranges.json
------
Decoding json...
Filter results...
Cleaning overlap networks...
Writting to files...
------

Stats: ipv4: 23(4171), ipv6: 12(1000)
```

### Arguments

| Alias | Argument                                 | Description                                 | Default value | Example values  |
|-------|------------------------------------------|---------------------------------------------|---------------|-----------------|
| -r    | --region REGION [REGION ...]             | Filter by region                            | None          | us-west-2-lax-1 |
| -g    | --network-border-group GROUP [GROUP ...] | Filter by network border group              | None          | eu-west-1       |
| -s    | --service SERVICE [SERVICE ...]          | Filter by service                           | None          | EC2 AMAZON      |
| -n    | --set4-name                              | Name for the set ipv4                       | awsip4        | --              |
| -N    | --set6-name                              | Name for the set ipv6                       | awsip6        | --              |
| -o    | --output-ipv4-file                       | File where the ipv4 set will be overwritted | aws-ipv4.nft  | --              |
| -O    | --output-ipv6-file                       | File where the ipv6 set will be overwritted | aws-ipv6.nft  | --              |

### Full example

Here is a example for block AWS IPS. The `aws.nft` file will be like this:

```nft
#!/usr/sbin/nft -f

table inet aws {

    include "./aws-ipv4.nft"
    include "./aws-ipv6.nft"

    chain aws-input {
        type filter hook input priority -1; policy accept;

        ip saddr @awsip4 drop
        ip6 saddr @awsip6 drop
    }

    chain input {
        type filter hook input priority 0; policy accept;
    }
}
```

## Credits

Most of the inspiration/copy of code is from [pvxe/nftables-geoip](https://github.com/pvxe/nftables-geoip) repository. Thanks to the author!
