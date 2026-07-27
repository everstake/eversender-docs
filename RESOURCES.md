# RPC
**Everstake Landing Tip Payment Accounts:**
- `J4cL8c22KNLHwheuWxK1SCYBWASWPGhEi6xvcGyf6o3S`
- `EzuhsszPxRUHBwGPXtKoqCB58EiTJ1QiYA2XrhbUEFbr`
- `7wsUm2VDopGDFyXkyhmgUh9V15QkEvnyqbgUPcagLcw2`
- `Cy3WAM9NdjFG3kXCxXmD17WmtJMBKVpoBXabkSm88Xdt`
- `BEEya88mme6JJ4rgshBR23eiDHmygUii9opUHE3qxnqK`
- `Gq21dPAGVuuZucqBQeCkfbbqoEowL1t88igZekJ93CRu`
- `79HFWkNoPhotXuFYi1ksuK5hE7AUnKasafP6c71hS9sM`
- `Cp4pCm5JjDaZ4gXB8eSjNJvQ8eg7uK6awgjveofrSATz`
- `DMHQ51qK2wChtDEUED54cqzbSLMLGvTygQCv5uLTUmZP`
- `GDnz7cAA7hKEFmDyrk6mz3drybHWc3Gn14y9LCsvvtjE`

**Everstake Landing RPC Endpoints**
- **Main Cloudflare**
  - `https://main-swqos.everstake.one`
  - `http://main-swqos.everstake.one`
- **FRA.**
  - `https://fra-swqos.everstake.one`
	- `http://fra-swqos.everstake.one`

- **NY.**
	- `https://ny-swqos.everstake.one`
	- `http://ny-swqos.everstake.one`

- **TYO.**
	- `https://tyo-swqos.everstake.one`
  - `http://tyo-swqos.everstake.one`

- **AMS.**
	- `https://ams-swqos.everstake.one`
  - `http://ams-swqos.everstake.one`
  
- **SGP.**
  - `https://sgp-swqos.everstake.one`
  - `http://sgp-swqos.everstake.one`
  
- **LON.**
  - `https://lon-swqos.everstake.one`
  - `http://lon-swqos.everstake.one`
  
**Important**: Always use the correct protocol (http/https) for the appropriate port. For example, port 443 only works with HTTPS.

**Min Lamports**
- `1000000`

**Special RPC Node Flag**
To send all transactions to a single leader (instead of following the leader schedule), the RPC node must include:
`--rpc-send-transaction-tpu-peer <SocketAddr>`
where `<SocketAddr>` is one of the Everstake Landing QUIC endpoints listed below.

# QUIC
To access **QUIC endpoints**, please refer to [this guide](https://docs.blockspace.everstake.one/swqos/quickstart#path-2--quic-lowest-latency-custom-client).
