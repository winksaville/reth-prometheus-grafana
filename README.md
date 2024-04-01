# reth-prometheus-grafana

My configuration for running prometheus and grafana on my
development computer, 3900x and have them being able to display
the reth metrics.

Currently Prometheus is able to run if you copy prometheus/prometheus.service
to /etc/systemd/system/ and then
`sudo systemctl daemon-reload && sudo systemctl start prometheus.service`. Of
course you'll need to change the `user`, `group` and ExecStart parameters to
approriate values in minimally 'wink` to your user name.

Also, change "targets: ['ethel:9001'] to the ipaddress andport of your reth node
and besure that the target is accessible from where ever prometheus is running,
for example this using `curl` to read from the the metrics port on the reth node
should yield a bunch of data, 6587 lines in my case:
```
wink@3900x 24-04-01T16:07:14.149Z:~/reth-prometheus-grafana (main)
$ curl -s ethel:9001 | wc -l
6587
wink@3900x 24-04-01T16:07:18.017Z:~/reth-prometheus-grafana (main)
```

What I did on Ethel to allow 3900x access `ethel:9001`:

## Update reth ExecStart parameter on Ethel:

So http ports are exposed to the LAN not just localhost by adding 
`--http.addr 0.0.0.0` to the ExecStart parameters in reth.service

## Configure firewall on Ethel:

I configured the firewall, using ufw, so the 3900x at 192.168.1.101
had access to 9001:
```
kendall@ethel 24-03-28T19:55:07.119Z:~/eth2-data/kendallstaking/service-files (main)
$ sudo ufw allow from 192.168.1.101 to any port 9001
Rule added
kendall@ethel 24-03-28T19:55:08.102Z:~/eth2-data/kendallstaking/service-files (main)
$ sudo ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 5052                       ALLOW IN    192.168.1.101             
[ 2] 5062                       ALLOW IN    192.168.1.101             
[ 3] 8545                       ALLOW IN    192.168.1.101             
[ 4] OpenSSH                    ALLOW IN    Anywhere                  
[ 5] 9001                       ALLOW IN    192.168.1.101             
[ 6] OpenSSH (v6)               ALLOW IN    Anywhere (v6)             

kendall@ethel 24-03-28T19:55:09.279Z:~/eth2-data/kendallstaking/service-files (main)
```

Grafana not tested yet, so it definitely doesn't work.

## License

Licensed under either of

- Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or http://apache.org/licenses/LICENSE-2.0)
- MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall
be dual licensed as above, without any additional terms or conditions.
