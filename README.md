# wg-admin

simple CLI tool(s?) for manipulating configuration on a WireGuard server from the command line. Initial push is to automate the creation of a new peer, adding it to the live wireguard config on the server (and adding a stanza for it to the server's config file for that interface), and returning a complete client config file on STDOUT.

## ¡¡¡ currently in super-duper alpha !!!
I am using this in **private** production right now, but it's really not up to my usual **public** production standards. If this code crashes your server or impregnates your cat, that's on you (and, well, the cat I suppose). 

This notice will be removed in a future version after the utility or utilities contained herein are cleaned up.

**There are a couple variables you must currently configure inside wg-addpeer!**

At minimum, you must set $ServerEndpoint to match the public IP and WireGuard port of your server, eg `127.0.0.1:51820` or similar. By default, clients will be allowed to access the entire subnet configured for the WireGuard interface specified on the CLI; you may append extra subnets to route over the wg interface by setting `$ServerExtraAllowedIPs`, or you may override it entirely by setting `$ServerOverrideAllowedIPs`.

There will eventually be a wg-admin.conf file or something to set this stuff in. (Did I mention we're in super-duper alpha right now...?)

**usage:** `wg-addpeer hostname interface`

  Run it on the WireGuard server, it'll generate a new keypair, add the new peer to the running 
  interface, add a peer stanza to /etc/[interface].conf, and spit out a complete set of configs
  to SDOUT for you to copy, then paste into a new WireGuard config file under /etc/wireguard 
  on the actual peer machine.
  
  
  
  Example test run:
  
    root@server:~# wg-addpeer demo-client wg0

    Commit new peer demo-client.wg with pubkey MWVq34u5QfKm7eVu/wWZHeoM3B6TcB1p4nZJGNcdnEM= to interface wg0? [Y/N] y
    
    Client config:
    
    [Interface]
	  # demo-client
    Address = 10.0.128.53/32
    PrivateKey = [redacted, pulled from running wg0 on server]
    SaveConfig = false
    
    [Peer]
    PublicKey = GRdmHtSSbSlsIbznaWibGOcbm1/Wni/PSZ4je07iLB0=
    PersistentKeepAlive = 20
    AllowedIPs = 10.0.128.0/24
    Endpoint = your.server.ip.address:yourwgport
    
    root@server:~# 

  If you want to remove a peer you've added, use the pubkey from the "commit?" query:
  
    root@server:~# wg set wg0 peer MWVq34u5QfKm7eVu/wWZHeoM3B6TcB1p4nZJGNcdnEM= remove
    
  Then `nano /etc/wireguard/wg0.conf` and remove its stanza from the config file (so it won't reappear at next restart of the interface).
  
  

