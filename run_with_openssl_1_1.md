# Run Ruby OpenSSL with OpenSSL 1.1

Referred to [this page](https://github.com/ruby/openssl/blob/master/CONTRIBUTING.md#with-different-versions-of-openssl).

Compiled OpenSSL on the openssl-3.1 branch latest commit `3a665e45b8b08957d1ba9228bf0c9c31cff074e5`.

Compiiled with the `enable-fips` option just in case. Prepared the `openssl_fips.cnf` to run it on the FIPS case.

```
$ /home/jaruga/.local/openssl-3.1.5-dev-fips-debug-3a665e45b8/bin/openssl version
OpenSSL 3.1.5-dev  (Library: OpenSSL 3.1.5-dev )

$ $HOME/.local/openssl-3.1.5-dev-fips-debug-3a665e45b8/bin/openssl list -providers
Providers:
  default
    name: OpenSSL Default Provider
    version: 3.1.5
    status: active

$ OPENSSL_CONF=$HOME/.local/openssl-3.1.5-dev-fips-debug-3a665e45b8/ssl/openssl_fips.cnf \
  $HOME/.local/openssl-3.1.5-dev-fips-debug-3a665e45b8/bin/openssl list -providers
Providers:
  base
    name: OpenSSL Base Provider
    version: 3.1.5
    status: active
  fips
    name: OpenSSL FIPS Provider
    version: 3.1.5
    status: active
```

Ran the Ruby OpenSSL on the latest master branch `8aa3849cffe58bf50031ac8f02c7fff311b5603d`.

```
$ pwd
/home/jaruga/git/ruby/openssl

$ which ruby
~/.local/ruby-3.4.0dev-debug-38bc107f0b/bin/ruby

$ bundle install

$ MAKEFLAGS="V=1" \
  RUBY_OPENSSL_EXTCFLAGS="-O0 -g3 -ggdb3 -gdwarf-5" \
  bundle exec rake compile -- \
  --with-openssl-dir="$HOME/.local/openssl-3.1.5-dev-fips-debug-3a665e45b8"
```

Printed the debugging information to check the OpenSSL installed from the source was used.

```
$ bundle exec rake debug
/home/jaruga/.local/ruby-3.4.0dev-debug-38bc107f0b/bin/ruby -I./lib -ropenssl -ve'openssl_version_number_str = OpenSSL::OPENSSL_VERSION_NUMBER.to_s(16)
libressl_version_number_str = (defined? OpenSSL::LIBRESSL_VERSION_NUMBER) ?
  OpenSSL::LIBRESSL_VERSION_NUMBER.to_s(16) : "undefined"
providers_str = (defined? OpenSSL::Provider) ?
  OpenSSL::Provider.provider_names.join(", ") : "undefined"
puts <<~MESSAGE
  OpenSSL::OPENSSL_VERSION: #{OpenSSL::OPENSSL_VERSION}
  OpenSSL::OPENSSL_LIBRARY_VERSION: #{OpenSSL::OPENSSL_LIBRARY_VERSION}
  OpenSSL::OPENSSL_VERSION_NUMBER: #{openssl_version_number_str}
  OpenSSL::LIBRESSL_VERSION_NUMBER: #{libressl_version_number_str}
  FIPS enabled: #{OpenSSL.fips_mode}
  Providers: #{providers_str}
MESSAGE
'
ruby 3.4.0dev (2024-01-09T09:47:15Z master 38bc107f0b) [x86_64-linux]
OpenSSL::OPENSSL_VERSION: OpenSSL 3.1.5-dev
OpenSSL::OPENSSL_LIBRARY_VERSION: OpenSSL 3.1.5-dev
OpenSSL::OPENSSL_VERSION_NUMBER: 30100050
OpenSSL::LIBRESSL_VERSION_NUMBER: undefined
FIPS enabled: false
Providers: default
```
