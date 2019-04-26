=====
Relay
=====

.. contents::    :depth: 3

Relay
=====

Usage
-----

Config
~~~~~~

To run a relay we need to build the contracts used for iden3:

::

    git clone https://github.com/iden3/contracts
    cd contracts
    npm install
    node_modules/.bin/truffle compile

Then we create a keystore protected by a password. First you need to
install `geth <https://geth.ethereum.org/downloads/>`__. Then run the
following commands with your password (we use THEPASSWORD here as an
example):

::

    echo "THEPASSWORD" > config-path/keystore.password
    geth account new --keystore config-path/keystore # Input the same password as the one stored in keystore.password

Then we will need to have a config file ``config.yaml`` with the
following data:

.. code:: yaml

    server:
      serviceapi: 127.0.0.1:8000
      adminapi: 127.0.0.1:8001

    web3:
      url: http://web3-gateway-url

    keystore:
      path: config-path/keystore
      address: 0xe0fbce58cfaa72812103f003adce3f284fe5fc7c
      password: config-path/keystore.password

    contracts:
      rootcommits:
        jsonabi: contracts-repo-path/build/contracts/RootCommits.json
        address: 0x6A6E04938d66Df5717ec4774E0ca181077e842ed
      iden3impl:
        jsonabi: contracts-repo-path/build/contracts/IDen3Impl.json
        address: 0x66D0c2F85F1B717168cbB508AfD1c46e07227130
      iden3deployer:
        jsonabi: contracts-repo-path/build/contracts/Deployer.json
        address: 0xf02e236F9F6C08966DD63B9fB9C04764E01b0563
      iden3proxy:
        jsonabi: contracts-repo-path/build/contracts/IDen3DelegateProxy.json

    storage:
      path: /tmp/treedb

    domain: iden3.io
    namespace: iden3.io

Running the relay
~~~~~~~~~~~~~~~~~

In the ``go-iden3/cmd/relay`` directory, run:

::

    go run main.go --config path-to-config/config.yaml start

Also can be built using:

::

    go build

And then execute:

::

    ./relay

API Endpoints
-------------

GET http://127.0.0.1:8000/api/v0.1/root
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Returns:

.. code:: js

    {
      "contractRoot": "0x0000000000000000000000000000000000000000000000000000000000000000",
      "root": "0x0458f3531f8292918aabef2d5f1c5a0c35da251c66c3f9d33eb4077e9ed0ec36"
    }

POST http://127.0.0.1:8000/api/v0.1/claim/:idaddr
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Input:

.. code:: js

    {
      "valueHex": "0x000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
    0000000000025521b25f396b1f62fcc46ce5b9a6b53684d5649958d83d79b5bb6711aa270000000000000000000000000000000000009105000000000000000000000004"
    ,
      "signatureHex": "0xd7cfe7c0935e27a6ce3c587da2f55a5f6765b859f57baddd22a232bf12563ac60cd91f6c1046acfd2c3d148f9d082e0ec194d72f3f1b2ead7985
    9809fa09bcae1c",
      "ksignpk": "0x03c2e48632c87932663beff7a1f6deb692cc61b041262ae8f310203d0f5ff57833"
    }

Returns:

.. code:: js

    {
      "proofClaim": {
        "proofs": [
          {
            "mtp0": "0x00030000000000000000000000000000000000000000000000000000000000041d9d41171c4b621ff279e2acb84d8ab45612fef53e37225bdf67e8
    ad761c3922",
            "mtp1": "0x0303000000000000000000000000000000000000000000000000000000000004294c2853becf85699f4d65fa57bd43e5c2e7087e23945d2c5ec52f
    903443139728f8267fb21e8ce0cdd9888a6e532764eb8d52dd6c1e354157c78b7ea281ce801541a6b5aa9bf7d9be3d5cb0bcc7cacbca26242016a0feebfc19c90f2224bae
    d",
            "root": "0x26815c474fa21c55dbef8e8628fc418946b147278f42402db7f07e4324ae9c5f",
            "aux": {
              "version": 1,
              "era": 0,
              "ethAddr": "0x7b471a1bdbd3b8ac98f3715507449f3a8e1f3b22"
            }
          },
          {
            "mtp0": "0x0001000000000000000000000000000000000000000000000000000000000001083dbb7700313075a2b8fe34b0188ff44784e3dc60987ed9277b59
    fad48f8199",
            "mtp1": "0x0301000000000000000000000000000000000000000000000000000000000001296c58506f1f3ecb09122a8eac285cd363840e2da8180d61188f0a
    c78189b96a182adc955c46e6629ac74027ded0c843c7c65e8c3c4f12f77add56500f9f402e25451237d9133b0f5c1386b7b822f382cb14c5fff612a913956ef5436fb6208
    a",
            "root": "0x141a1d2dceec7ff08497d15fc092f18ac460c8654ff9fed6626c1d66eeb3c75b",
            "aux": null
          }
        ],
        "leaf": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
    00000000025521b25f396b1f62fcc46ce5b9a6b53684d5649958d83d79b5bb6711aa270000000000000000000000000000000000009105000000000000000000000004",
        "date": 1548849932,
        "signature": "0x224dca4c57fb4c4bb946ec1ba82cf46d2f12da5a1a73fe143bfcd3ae20212975519c9d711ce2c4e414eae950b28be741e6b9721cd663d71fb2a48
    44efa5a84ed00"
      }
    }

GET http://127.0.0.1:8000/api/v0.1/claim/:idaddr/root
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Returns:

.. code:: js

    {
      "idRoot": "0x0000000000000000000000000000000000000000000000000000000000000000",
      "idRootProof": "0x01020000000000000000000000000000000000000000000000000000000000030df6a62218b641b022bbd990303ec57411ebfe24965af84a7d3e4
    dc8e92d46bb2c7df576dfac28d7b2a9a534e1d099e0438f04f66bebaa03a2349860d26e2e62",
      "root": "0x0458f3531f8292918aabef2d5f1c5a0c35da251c66c3f9d33eb4077e9ed0ec36"
    }

GET http://127.0.0.1:8000/api/v0.1/claim\_proof/idaddr/:idaddr/hi/:hi
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

4eb8d52dd6c1e354157c78b7ea281ce80 Returns:

.. code:: js

    {
      "proofClaim": {
        "proofs": [
          {
            "mtp0": "0x00030000000000000000000000000000000000000000000000000000000000051b12c5489d45a9759a0aa761b4031fc4fa4afac3d6315273eecd13
    58d562b9de294c2853becf85699f4d65fa57bd43e5c2e7087e23945d2c5ec52f9034431397",
            "mtp1": "0x03030000000000000000000000000000000000000000000000000000000000051b12c5489d45a9759a0aa761b4031fc4fa4afac3d6315273eecd13
    58d562b9de1d9d41171c4b621ff279e2acb84d8ab45612fef53e37225bdf67e8ad761c39221a81d39b8b3f86e7a4b3df400dcb541f478df56414d3bd0d4b3cfa2f8e7df07
    c1541a6b5aa9bf7d9be3d5cb0bcc7cacbca26242016a0feebfc19c90f2224baed",
            "root": "0x1a99534d2fad42577649c8fa0af4c2b5610f316f7bf29814ba36a2c4f1e76c21",
            "aux": {
              "version": 2,
              "era": 0,
              "ethAddr": "0x7b471a1bdbd3b8ac98f3715507449f3a8e1f3b22"
            }
          },
          {
            "mtp0": "0x00020000000000000000000000000000000000000000000000000000000000031744e6cadba4793eacdfb8d32e955ea12f976b72cef88059e09bb5
    f6ea5d9de0083dbb7700313075a2b8fe34b0188ff44784e3dc60987ed9277b59fad48f8199",
            "mtp1": "0x01020000000000000000000000000000000000000000000000000000000000030df6a62218b641b022bbd990303ec57411ebfe24965af84a7d3e4d
    c8e92d46bb2c7df576dfac28d7b2a9a534e1d099e0438f04f66bebaa03a2349860d26e2e62",
            "root": "0x0458f3531f8292918aabef2d5f1c5a0c35da251c66c3f9d33eb4077e9ed0ec36",
            "aux": null
          }
        ],
        "leaf": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
    0000000003c2e48632c87932663beff7a1f6deb692cc61b041262ae8f310203d0f5ff50000000000000000000000000000000000007833000000000000000000000004",
        "date": 1548849932,
        "signature": "0xd18b60beb56a40dcb4ad5648d3b7d122137aa75f96c858cd1e8f0999cb02f35255897d9a6cbbf9df02745c36e6ac4ad2a7a839b81ae4941bcbd4c
    0136cb76b5200"
      }
    }

POST http://127.0.0.1:8000/api/v0.1/id
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Input:

.. code:: js

    {
      "operationalpk": "0x03c2e48632c87932663beff7a1f6deb692cc61b041262ae8f310203d0f5ff57833",
      "recoverer": "0xf3c9f94e4eaffef676d4fd3b4fc2732044caea91",
      "revokator": "0xb07079bd6238fa845dc77bbce3ec2edf98ffe735"
    }

Returns:

.. code:: js

    {
      "idaddr": "0x7b471a1bdbd3b8ac98f3715507449f3a8e1f3b22",
      "proofClaim": {
        "proofs": [
          {
            "mtp0": "0x0000000000000000000000000000000000000000000000000000000000000000",
            "mtp1": "0x030000000000000000000000000000000000000000000000000000000000000028f8267fb21e8ce0cdd9888a6e532764eb8d52dd6c1e354157c78b
    7ea281ce801541a6b5aa9bf7d9be3d5cb0bcc7cacbca26242016a0feebfc19c90f2224baed",
            "root": "0x1d9d41171c4b621ff279e2acb84d8ab45612fef53e37225bdf67e8ad761c3922",
            "aux": {
              "version": 0,
              "era": 0,
              "ethAddr": "0x7b471a1bdbd3b8ac98f3715507449f3a8e1f3b22"
            }
          },
          {
            "mtp0": "0x0000000000000000000000000000000000000000000000000000000000000000",
            "mtp1": "0x0300000000000000000000000000000000000000000000000000000000000000182adc955c46e6629ac74027ded0c843c7c65e8c3c4f12f77add56
    500f9f402e25451237d9133b0f5c1386b7b822f382cb14c5fff612a913956ef5436fb6208a",
            "root": "0x083dbb7700313075a2b8fe34b0188ff44784e3dc60987ed9277b59fad48f8199",
            "aux": null
          }
        ],
        "leaf": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
    0000000003c2e48632c87932663beff7a1f6deb692cc61b041262ae8f310203d0f5ff50000000000000000000000000000000000007833000000000000000000000004",
        "date": 1548849932,
        "signature": "0x65312b0604555dd6a406e394d2174bae040a22c13143d3f97b282d55619315765e4fb4f783aa4c26979dc9bbe51ff6c17c1176f57c140a3120e3e
    3d2f9044f1001"
      }
    }

GET http://127.0.0.1:8000/api/v0.1/id/0x7b471a1bdbd3b8ac98f3715507449f3a8e1f3b22
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Returns:

.. code:: js

    {
      "IdAddr": "0x7b471a1bdbd3b8ac98f3715507449f3a8e1f3b22",
      "LocalDb": {
        "Operational": "0xc7d89fe96acdb257b434bf580b8e6eb677d445a9",
        "OperationalPk": "0x03c2e48632c87932663beff7a1f6deb692cc61b041262ae8f310203d0f5ff57833",
        "Relayer": "0xe0fbce58cfaa72812103f003adce3f284fe5fc7c",
        "Recoverer": "0xf3c9f94e4eaffef676d4fd3b4fc2732044caea91",
        "Revokator": "0xb07079bd6238fa845dc77bbce3ec2edf98ffe735",
        "Impl": "0x66d0c2f85f1b717168cbb508afd1c46e07227130"
      },
      "Onchain": {
        "Codehash": "0x4fec321ffcfdd48cdbe4d02553acb18ddb04cd5c6a78bcaf86e87834b1f3d0ee",
        "Impl": "0x66d0c2f85f1b717168cbb508afd1c46e07227130",
        "Recoverer": "0xf3c9f94e4eaffef676d4fd3b4fc2732044caea91",
        "RecovererProp": "0x0000000000000000000000000000000000000000",
        "Revoker": "0xb07079bd6238fa845dc77bbce3ec2edf98ffe735",
        "Relay": "0xe0fbce58cfaa72812103f003adce3f284fe5fc7c",
        "LastNonce": 0
      }
    }

POST http://127.0.0.1:8000/api/v0.1/id/:idaddr/deploy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Input:

.. code:: js

    {}

Returns:

.. code:: js

    {
      idaddr: '0x8435ebb41634c05019be1710be0007fa0d92861f',
      tx: '0x403859ccc701eb358d3a25c908c33de733cbb2d0ebc1c7738eed4908cc8cf5c4'
    }

POST http://127.0.0.1:8000/api/v0.1/vinculateid
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Input:

.. code:: js

    {
      "ethAddr": "0x7b471a1bdbd3b8ac98f3715507449f3a8e1f3b22",
      "name": "testName",
      "signature": "0x8526016fb5f0fda5d04b37725768a82f17c7886541445304730bcf021c96e5ce6181b3a6e1d1ca4faa68f802d169514664f576d006fe872e646c96a
    e9a75d6c11c",
      "ksignpk": "0x03c2e48632c87932663beff7a1f6deb692cc61b041262ae8f310203d0f5ff57833"
    }

Returns:

.. code:: js

    {
      "claimAssignName": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000007b471a1bdbd3b8ac98f371550
    7449f3a8e1f3b22008c8efcda9e563cf153563941b60fc5ac88336fc58d361eb0888686fadb99760000000000000000000000000000000000000000000000000000000000
    000003",
      "ethAddr": "0x7b471a1bdbd3b8ac98f3715507449f3a8e1f3b22",
      "name": "testName",
      "proofClaimAssignName": {
        "proofs": [
          {
            "mtp0": "0x00070000000000000000000000000000000000000000000000000000000000410df6a62218b641b022bbd990303ec57411ebfe24965af84a7d3e4d
    c8e92d46bb296c58506f1f3ecb09122a8eac285cd363840e2da8180d61188f0ac78189b96a",
            "mtp1": "0x03020000000000000000000000000000000000000000000000000000000000031744e6cadba4793eacdfb8d32e955ea12f976b72cef88059e09bb5
    f6ea5d9de0083dbb7700313075a2b8fe34b0188ff44784e3dc60987ed9277b59fad48f8199137896c5dde3243fba9080cd9eb1aad51a293091da7afd15b05f54a82a4633a
    c10b8436ad110ba4812e91e282ef9ef833006cda841f9121345a2eb8f76ed09bd",
            "root": "0x0458f3531f8292918aabef2d5f1c5a0c35da251c66c3f9d33eb4077e9ed0ec36",
            "aux": null
          }
        ],
        "leaf": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000007b471a1bdbd3b8ac98f3715507449f3a8e
    1f3b22008c8efcda9e563cf153563941b60fc5ac88336fc58d361eb0888686fadb99760000000000000000000000000000000000000000000000000000000000000003",
        "date": 1548849932,
        "signature": "0xd18b60beb56a40dcb4ad5648d3b7d122137aa75f96c858cd1e8f0999cb02f35255897d9a6cbbf9df02745c36e6ac4ad2a7a839b81ae4941bcbd4c
    0136cb76b5200"
      }
    }

GET http://127.0.0.1:8000/api/v0.1/identities/resolv/testName@iden3.io
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Returns:

.. code:: js

    {
      "claim": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000007b471a1bdbd3b8ac98f3715507449f3a8e1
    f3b22008c8efcda9e563cf153563941b60fc5ac88336fc58d361eb0888686fadb99760000000000000000000000000000000000000000000000000000000000000003",
      "ethAddr": "0x7b471a1bdbd3b8ac98f3715507449f3a8e1f3b22",
      "proofClaimAssignName": {
        "proofs": [
          {
            "mtp0": "0x00070000000000000000000000000000000000000000000000000000000000410df6a62218b641b022bbd990303ec57411ebfe24965af84a7d3e4d
    c8e92d46bb296c58506f1f3ecb09122a8eac285cd363840e2da8180d61188f0ac78189b96a",
            "mtp1": "0x03020000000000000000000000000000000000000000000000000000000000031744e6cadba4793eacdfb8d32e955ea12f976b72cef88059e09bb5
    f6ea5d9de0083dbb7700313075a2b8fe34b0188ff44784e3dc60987ed9277b59fad48f8199137896c5dde3243fba9080cd9eb1aad51a293091da7afd15b05f54a82a4633a
    c10b8436ad110ba4812e91e282ef9ef833006cda841f9121345a2eb8f76ed09bd",
            "root": "0x0458f3531f8292918aabef2d5f1c5a0c35da251c66c3f9d33eb4077e9ed0ec36",
            "aux": null
          }
        ],
        "leaf": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000007b471a1bdbd3b8ac98f3715507449f3a8e
    1f3b22008c8efcda9e563cf153563941b60fc5ac88336fc58d361eb0888686fadb99760000000000000000000000000000000000000000000000000000000000000003",
        "date": 1548849932,
        "signature": "0xd18b60beb56a40dcb4ad5648d3b7d122137aa75f96c858cd1e8f0999cb02f35255897d9a6cbbf9df02745c36e6ac4ad2a7a839b81ae4941bcbd4c
    0136cb76b5200"
      }
    }
