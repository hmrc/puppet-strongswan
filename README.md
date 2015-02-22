# puppet-strongswan

This Puppet module contains configurations for strongSwan.

## Example usage

strongSwan can be installed by simply doing:

```puppet
include strongswan
```

### Default configuration

*conn %default* configurations can be set as follows:

```puppet
strongswan::conn { '%default':
  options => {
    "ike"         => "aes128gcm128-prfsha256-ecp256!",
    "esp"         => "aes128gcm128-ecp256!",
    "keyexchange" => "ikev2",
  }
}
```

### Peer configuration

Parameters for an IPsec peer:

```puppet
strongswan::conn { 'peer':
  options => {
    "left"         => "10.0.1.1",
    "leftcert"     => 'peerCert.der',
    "leftfirewall" => 'no',
    "leftid"       => '"C=UK, CN=Peer 1"',
    "leftsubnet"   => "10.0.1.0/24",
    "right"        => '10.0.2.1',
    "rightauth"    => 'pubkey',
    "rightid"      => '"C=UK, CN=Peer 2"',
    "rightsubnet"  => '10.0.2.0/24',
    "auto"         => "start",
  }
}

strongswan::secrets { 'peer':
  options => {
    'ECDSA'        => 'peerKey.der',
}
```

### Gateway configuration

Parameters for an IPsec gateway server:
```puppet
strongswan::conn { 'gateway':
  options => {
    "left "         => '%any',
    "leftcert"      => 'gwCert.der',
    "leftfirewall"  => "yes",
    "leftid"        => '"C=UK, CN=GW"',
    "leftsubnet"    => '10.0.0.0/24',
    "right"         => '%any',
    "rightauth"     => "pubkey",
    "rightsourceip" => '10.0.1.0/24',
  }
}

strongswan::secrets { 'peer':
  options => {
  'ECDSA'         => 'gwKey.der',
}
```

Gateway charon configuration:

```puppet
class { 'strongswan::charon':
  dns1                  => "10.0.0.5",
  initiator_only        => "no",
  integrity_test        => "yes",
}
```

### Roadwarrior configuration

Parameters for an IPsec roadwarrior connection:

```puppet
strongswan::conn { 'roadwarrior':
  options => {
    "keyingtries"  => "%forever",
    "leftcert"     => 'rwCert.der',
    "leftid"       => '"C=UK, CN=rw"',
    "right"        => '10.0.0.1',
    "rightid"      => '"C=UK, CN=GW"',
    "rightsubnet"  => '0.0.0.0/0',
  }
}

strongswan::secrets { 'roadwarrior':
  options => {
  'ECDSA'        => 'rwKey.der',
}
```

charon daemon configuration can also be adjusted, for example, for a client
configuration:

```puppet
class { 'strongswan::charon':
  initiator_only        => "yes",
  integrity_test        => "yes",
  crypto_test_on_add    => "yes",
  crypto_test_on_create => "yes",
  crypto_test_required  => "yes",
}
```

## License

See [LICENSE](LICENSE) file.
