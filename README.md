# Release 4.0.1 - (July 3, 2020)
 * Lightning Network support (experimental)
   - Our implementation of Lightning relies on Electrum servers to
     query channel states. Since servers can lie about the state of a
     channel, users should either use a server that they trust, or
     setup a private watchtower (see below). A watchtower is also
     recommended for lightning wallets that remain offline for
     extended periods of time (the default CSV 'to_self_delay' is 1
     week). Please note that Electrum Personal Server (EPS) cannot be
     used with lightning wallets, because channels funding addresses
     are arbitrary.
   - Lightning funds cannot be restored from seed. Instead, users need
     to create static backups of their channels. Static backups cannot
     be used to perform lightning transactions, they can only be used
     to trigger a remote-force-close of a channel.
   - Lightning-enabled wallet files must not be copied. Instead, a
     backup of the wallet can be created from the Qt menu, and it will
     contain static backups of all its channels. Backups can also be
     exported for each channel (e.g. via QR code), and imported in
     another wallet. Since backups are encrypted with a key derived
     from the wallet's xpub, they can only be imported into another
     instance of the same wallet, or a watch-only version of it. The
     force-close is not triggered automatically when the backup is
     imported; imported backups can live inside a wallet file.
   - Lightning can be enabled in the GUI (Wallet>Information) or from
     the CLI (init_lightning). Lightning is currently restricted to HD
     p2wpkh wallets (including watch-only and hardware wallets). The
     Qt GUI, CLI/RPC, and the kivy GUI (Android) all have LN support,
     with feature-richness in that order.
   - LN protocol details: dataloss_protect and static_remotekey are
     required; varonion and payment_secret are implemented, MPP not yet.
     Channels are not announced ('private'), forwarding is disabled.
     We do not serve gossip queries, only consume them.
   - Submarine swaps: the GUI integrates a service that offers
     atomically exchanging on-chain and lightning bitcoins for a fee.
     Electrum Technologies runs a central server for this, powered by
     the Boltz backend.
   - Watchtowers: Electrum can run a local watchtower (GUI setting),
     or it can connect to a remote watchtower. A watchtower contains
     pre-signed transactions and does not need your private keys. A
     local watchtower will watch your channels whenever an Electrum
     instance is running, without needing access to your wallet file.
     An Electrum daemon can be configured to be used as a remote
     watchtower by setting 'watchtower_address', 'watchtower_user' and
     'watchtower_password'.
 * Partially Signed Bitcoin Transactions (PSBT, BIP-174) are supported
   (#5721). The previous Electrum partial transaction format is no
   longer supported, i.e. this is an incompatible change. Users should
   make sure that all instances of Electrum they use to co-sign or
   offline sign, are updated together.
 * Hardware wallets: several fixes in general; notable changes:
   - The BitBox02 is now supported (#5993)
   - Multisig support for Coldcard (#5440)
   - Compatibility with latest Trezor fw (#6064, #6198, #5692)
 * Dependencies (see README for install instructions):
   - libsecp256k1 is now required (previously optional). python-ecdsa
     remains a dependency but it is now only used for DNSSEC.
   - Added: either one of pycryptodomex or cryptography is now required,
     mainly due to LN (previously pycryptodomex was optional, for fast AES)
   - Removed: jsonrpclib-pelix, the JSON-RPC library used for CLI/daemon
 * Qt GUI: several changes, notably:
   - Separation between output selection and transaction finalization.
   - Coin selection moved to the Coins tab, and it affects all txns,
     e.g. RBF fee-bumping, LN channel opens, submarine swaps.
   - Editable tx preview dialog that allows e.g. changing the locktime,
     toggling RBF, and manual coinjoins.
 * HTTP PayServer: The configuration of a bitcoin-accepting website
   using Electrum has been simplified and requires fewer steps (see
   documentation). The Payserver supports BIP70 and Lightning payments.
 * Android:
   - We now build two APKs, one for ARMv7 and one for ARMv8
   - The kivy GUI now supports importing BIP39 seeds
   - Each wallet on kivy now can have a separate generic password,
     using which the wallet files are encrypted. An optional PIN,
     shared among all wallets, can be added to get prompted for spends.
 * The API of several CLI/RPC commands have changed, and several new
   commands have been introduced (mainly for LN).
 * Distributables:
   - The .tar.gz source dist is now built reproducibly.
     Relatedly, we no longer distribute a .zip sdist.
   - The MacOS binary now conforms to macOS 10.15; it is notarized
     by Apple. This required bumping the min macOS version to 10.13.
     Startup times should now be faster on 10.15. (#6128, #6225)
 * Transactions:
   - we now grind low R for ECDSA signatures to match bitcoind (#5820)
 * Lots and lots of other minor bugfixes and improvements.


# Release 3.3.8 - (July 11, 2019)

 * fix some bugs with recent bump fee (RBF) improvements (#5483, #5502)
 * fix #5491: watch-only wallets could not bump fee in some cases
 * appimage: URLs could not be opened on some desktop environments (#5425)
 * faster tx signing for segwit inputs for really large txns (#5494)
 * A few other minor bugfixes and usability improvements.


# Release 3.3.7 - (July 3, 2019)

 * The AppImage Linux x86_64 binary and the Windows setup.exe
   (so now all Windows binaries) are now built reproducibly.
 * Bump fee (RBF) improvements:
   Implemented a new fee-bump strategy that can add new inputs,
   so now any tx can be fee-bumped (d0a4366). The old strategy
   was to decrease the value of outputs (starting with change).
   We will now try the new strategy first, and only use the old
   as a fallback (needed e.g. when spending "Max").
 * CoinChooser improvements:
   - more likely to construct txs without change (when possible)
   - less likely to construct txs with really small change (e864fa5)
   - will now only spend negative effective value coins when
     beneficial for privacy (cb69aa8)
 * fix long-standing bug that broke wallets with >65k addresses (#5366)
 * Windows binaries: we now build the PyInstaller boot loader ourselves,
   as this seems to reduce anti-virus false positives (1d0f679)
 * Android: (fix) BIP70 payment requests could not be paid (#5376)
 * Android: allow copy-pasting partial transactions from/to clipboard
 * Fix a performance regression for large wallets (c6a54f0)
 * Qt: fix some high DPI issues related to text fields (37809be)
 * Trezor:
   - allow bypassing "too old firmware" error (#5391)
   - use only the Bridge to scan devices if it is available (#5420)
 * hw wallets: (known issue) on Win10-1903, some hw devices
   (that also have U2F functionality) can only be detected with
   Administrator privileges. (see #5420 and #5437)
   A workaround is to run as Admin, or for Trezor to install the Bridge.
 * Several other minor bugfixes and usability improvements.


# Release 3.3.6 - (May 16, 2019)

 * qt: fix crash during 2FA wallet creation (#5334)
 * fix synchronizer not to keep resubscribing to addresses of
   already closed wallets (e415c0d9)
 * fix removing addresses/keys from imported wallets (#4481)
 * kivy: fix crash when aborting 2FA wallet creation (#5333)
 * kivy: fix rare crash when changing exchange rate settings (#5329)
 * A few other minor bugfixes and usability improvements.


# Release 3.3.5 - (May 9, 2019)

 * The logging system has been overhauled (#5296).
   Logs can now also optionally be written to disk, disabled by default.
 * Fix a bug in synchronizer (#5122) where client could get stuck.
   Also, show the progress of history sync in the GUI. (#5319)
 * fix Revealer in Windows and MacOS binaries (#5027)
 * fiat rate providers:
   - added CoinGecko.com and CoinCap.io
   - BitcoinAverage now only provides historical exchange rates for
     paying customers. Changed default provider to CoinGecko.com (#5188)
 * hardware wallets:
   - Ledger: Nano X is now recognized (#5140)
   - KeepKey:
     - device was not getting detected using Windows binary (#5165)
     - support firmware 6.0.0+ (#5205)
   - Trezor: implemented "seedless" mode (#5118)
 * Coin Control in Qt: implemented freezing individual UTXOs
   in addition to freezing addresses (#5152)
 * TrustedCoin (2FA wallets):
   - better error messages (#5184)
   - longer signing timeout (#5221)
 * Kivy:
   - fix bug with local transactions (#5156)
   - allow selecting fiat rate providers without historical data (#5162)
 * fix CPFP: the fees already paid by the parent were not included in
   the calculation, so it always overestimated (#5244)
 * Testnet: there is now a warning when the client is started in
   testnet mode as there were a number of reports of users getting
   scammed through social engineering (#5295)
 * CoinChooser: performance of creating transactions has been improved
   significantly for large wallets. (d56917f4)
 * Importing/sweeping WIF keys: stricter checks (#4638, #5290)
 * Electrum protocol: the client's "user agent" has been changed from
   "3.3.5" to "electrum/3.3.5". Other libraries connecting to servers
   can consider not "spoofing" to be Electrum. (#5246)
 * Several other minor bugfixes and usability improvements.


# Release 3.3.4 - (February 13, 2019)

 * AppImage: we now also distribute self-contained binaries for x86_64
   Linux in the form of an AppImage (#5042). The Python interpreter,
   PyQt5, libsecp256k1, PyCryptodomex, zbar, hidapi/libusb (including
   hardware wallet libraries) are all bundled. Note that users of
   hw wallets still need to set udev rules themselves.
 * hw wallets: fix a regression during transaction signing that prompts
   the user too many times for confirmations (commit 2729909)
 * transactions now set nVersion to 2, to mimic Bitcoin Core
 * fix Qt bug that made all hw wallets unusable on Windows 8.1 (#4960)
 * fix bugs in wallet creation wizard that resulted in corrupted
   wallets being created in rare cases (#5082, #5057)
 * fix compatibility with Qt 5.12 (#5109)


# Release 3.3.3 - (January 25, 2019)

 * Do not expose users to server error messages (#4968)
 * Notify users of new releases. Release announcements must be signed,
   and they are verified byElectrum using a hardcoded Bitcoin address.
 * Hardware wallet fixes (#4991, #4993, #5006)
 * Display only QR code in QRcode Window
 * Fixed code signing on MacOS
 * Randomise locktime of transactions


# Release 3.3.2 - (December 21, 2018)

 * Fix Qt history export bug
 * Improve network timeouts
 * Prepend server transaction_broadcast error messages with
   explanatory message. Render error messages as plain text.


# Release 3.3.1 - (December 20, 2018)

 * Qt: Fix invoices tab crash (#4941)
 * Android: Minor GUI improvements


# Release 3.3.0 - Hodler's Edition (December 19, 2018)

 * The network layer has been rewritten using asyncio and aiorpcx.
   In addition to easier maintenance, this makes the client
   more robust against misbehaving servers.
 * The minimum python version was increased to 3.6
 * The blockchain headers and fork handling logic has been generalized.
   Clients by default now follow chain based on most work, not length.
 * New wallet creation defaults to native segwit (bech32).
 * Segwit 2FA: TrustedCoin now supports native segwit p2wsh
   two-factor wallets.
 * RBF batching (opt-in): If the wallet has an unconfirmed RBF
   transaction, new payments will be added to that transaction,
   instead of creating new transactions.
 * MacOS: support QR code scanner in binaries.
 * Android APK:
   - build using Google NDK instead of Crystax NDK
   - target API 28
   - do not use external storage (previously for block headers)
 * hardware wallets:
   - Coldcard now supports spending from p2wpkh-p2sh,
     fixed p2pkh signing for fw 1.1.0
   - Archos Safe-T mini: fix #4726 signing issue
   - KeepKey: full segwit support
   - Trezor: refactoring and compat with python-trezor 0.11
   - Digital BitBox: support firmware v5.0.0
 * fix bitcoin URI handling when app already running (#4796)
 * Qt listings rewritten:
   the History tab now uses QAbstractItemModel, the other tabs use
   QStandardItemModel. Performance should be better for large wallets.
 * Several other minor bugfixes and usability improvements.


# Release 3.2.3 - (September 3, 2018)

 * hardware wallet: the Safe-T mini from Archos is now supported.
 * hardware wallet: the Coldcard from Coinkite is now supported.
 * BIP39 seeds: if a seed extension (aka passphrase) contained
   multiple consecutive whitespaces or leading/trailing whitespaces
   then the derived addresses were not following spec. This has been
   fixed, and affected should move their coins. The wizard will show a
   warning in this case. (#4566)
 * Revealer: the PRNG used has been changed (#4649)
 * fix Linux distributables: 'typing' was not bundled, needed for python 3.4
 * fix #4626: fix spending from segwit multisig wallets involving a Trezor
   cosigner when using a custom derivation path
 * fix #4491: on Android, if user had set "uBTC" as base unit, app crashed
 * fix #4497: on Android, paying bip70 invoices from cold start did not work
 * Several other minor bugfixes and usability improvements.


# Release 3.2.2 - (July 2nd, 2018)

 * Fix DNS resolution on Windows
 * Fix websocket bug in daemon


# Release 3.2.1 - (July 1st, 2018)

 * fix Windows binaries: due to build process changes, the locale files
   were not included; the language could not be changed from English
 * fix Linux distributables: wordlists were not included (#4475)


# Release 3.2.0 - Satoshi's Vision (June 30, 2018)

 * If present, libsecp256k1 is used to speed up elliptic curve
   operations. The library is bundled in the Windows, MacOS, and
   Android binaries. On Linux, it needs to be installed separately.
 * Two-factor authentication is available on Android. Note that this
   will only provide additional security if one time passwords are
   generated on a separate device.
 * Semi-automated crash reporting is implemented for Android.
 * Transactions that are dropped from the mempool are kept in the
   wallet as 'local', and can be rebroadcast. Previously these
   transactions were deleted from the wallet.
 * The scriptSig and witness part of transaction inputs are no longer
   parsed, unless actually needed. The wallet will no longer display
   'from' addresses corresponding to transaction inputs, except for
   its own inputs.
 * The partial transaction format has been incompatibly changed. This
   was needed as for partial transactions the scriptSig/witness has to
   be parsed, but for signed transactions we did not want to do the
   parsing.  Users should make sure that all instances of Electrum
   they use to co-sign or offline sign, are updated together.
 * Signing of partial transactions created with online imported
   addresses wallets now supports significantly more
   setups. Previously only online p2pkh address + offline WIF was
   supported.  Now the following setups are all supported:
   - online {p2pkh, p2wpkh-p2sh, p2wpkh} address + offline WIF,
   - online {p2pkh, p2wpkh-p2sh, p2wpkh} address + offline seed/xprv,
   - online {p2sh, p2wsh-p2sh, p2wsh}-multisig address + offline seeds/xprvs
     (potentially distributed among several different machines)
   Note that for the online address + offline HD secret case, you need
   the offline wallet to recognize the address (i.e. within gap
   limit).  Having an xpub on the online machine is still the
   recommended setup, as this allows the online machine to generate
   new addresses on demand.
 * Segwit multisig for bip39 and hardware wallets is now enabled.
   (both p2wsh-p2sh and native p2wsh)
 * Ledger: offline signing for segwit inputs (#3302) This has already
   worked for Trezor and Digital Bitbox. Offline segwit signing can be
   combined with online imported addresses wallets.
 * Added Revealer plugin. ( https://revealer.cc ) Revealer is a seed
   phrase back-up solution. It allows you to create a cold, analog,
   multi-factor backup of your wallet seeds, or of any arbitrary
   secret. The Revealer utilizes a transparent plastic visual one time
   pad.
 * Fractional fee rates: the Qt GUI now displays fee rates with 0.1
   sat/byte precision, and also allows this same resolution in the
   Send tab.
 * Hardware wallets: a "show address" button is now displayed in the
   Receive tab of the Qt GUI. (#4316)
 * Trezor One: implemented advanced/matrix recovery (#4329)
 * Qt/Kivy: added "sat" as optional base unit.
 * Kivy GUI: significant performance improvements when displaying
   history and address list of large wallets; and transaction dialog
   of large transactions.
 * Windows: use dnspython to resolve dns instead of socket.getaddrinfo
   (#4422)
 * Importing minikeys: use uncompressed pubkey instead of compressed
   (#4384)
 * SPV proofs: check inner nodes not to be valid transactions (#4436)
 * Qt GUI: there is now an optional "dark" theme (#4461)
 * Several other minor bugfixes and usability improvements.


# Release 3.1.3 - (April 16, 2018)

 * Qt GUI: seed word auto-complete during restore
 * Android: fix some crashes
 * performance improvements (wallet, and Qt GUI)
 * hardware wallets: show debug message during device scan
 * Digital Bitbox: enabled BIP84 (p2wpkh) wallet creation
 * add regtest support (via --regtest flag)
 * other minor bugfixes and usability improvements

# Release 3.1.2 - (March 28, 2018)

 * Kivy/android: request PIN on startup
 * Improve OSX build process
 * Fix various bugs with hardware wallets
 * Other minor bugfixes

# Release 3.1.1 - (March 12, 2018)

 * fix #4031: Trezor T support
 * partial fix #4060: proxy and hardware wallet can't be used together
 * fix #4039: can't set address labels
 * fix crash related to coinbase transactions
 * MacOS: use internal graphics card
 * fix openalias related crashes
 * speed-up capital gains calculations
 * hw wallet encryption: re-prompt for passphrase if incorrect
 * other minor fixes.



# Release 3.1.0 - (March 5, 2018)

 * Memory-pool based fee estimation. Dynamic fees can target a desired
   depth in the memory pool. This feature is optional, and ETA-based
   estimates from Bitcoin Core are still available. Note that miners
   could exploit this feature, if they conspired and filled the memory
   pool with expensive transactions that never get mined. However,
   since the Electrum client already trusts an Electrum server with
   fee estimates, activating this feature does not introduce any new
   vulnerability. In addition, the client uses a hard threshold to
   protect itself from servers sending excessive fee estimates. In
   practice, ETA-based estimates have resulted in sticky fees, and
   caused many users to overpay for transactions. Advanced users tend
   to visit (and trust) websites that display memory-pool data in
   order to set their fees.
 * Capital gains: For each outgoing transaction, the difference
   between the acquisition and liquidation prices of outgoing coins is
   displayed in the wallet history. By default, historical exchange
   rates are used to compute acquisition and liquidation prices. These
   values can also be entered manually, in order to match the actual
   price realized by the user. The order of liquidation of coins is
   the natural order defined by the blockchain; this results in
   capital gain values that are invariant to changes in the set of
   addresses that are in the wallet. Any other ordering strategy (such
   as FIFO, LIFO) would result in capital gain values that depend on
   the presence of other addresses in the wallet.
 * Local transactions: Transactions can be saved in the wallet without
   being broadcast. The inputs of local transactions are considered as
   spent, and their change outputs can be re-used in subsequent
   transactions. This can be combined with cold storage, in order to
   create several transactions before broadcasting them. Outgoing
   transactions that have been removed from the memory pool are also
   saved in the wallet, and can be broadcast again.
 * Checkpoints: The initial download of a headers file was replaced
   with hardcoded checkpoints. The wallet uses one checkpoint per
   retargeting period. The headers for a retargeting period are
   downloaded only if transactions need to be verified in this period.
 * The 'privacy' and 'priority' coin selection policies have been
   merged into one. Previously, the 'privacy' policy has been unusable
   because it was was not prioritizing confirmed coins. The new policy
   is similar to 'privacy', except that it de-prioritizes addresses
   that have unconfirmed coins.
 * The 'Send' tab of the Qt GUI displays how transaction fees are
   computed from transaction size.
 * The wallet history can be filtered by time interval.
 * Replace-by-fee is enabled by default. Note that this might cause
   some issues with wallets that do not display RBF transactions until
   they are confirmed.
 * Watching-only wallets and hardware wallets can be encrypted.
 * Semi-automated crash reporting
 * The SSL checkbox option was removed from the GUI.
 * The Trezor T hardware wallet is now supported.
 * BIP84: native segwit p2wpkh scripts for bip39 seeds and hardware
   wallets can now be created when specifying a BIP84 derivation
   path. This is usable with Trezor and Ledger.
 * Windows: the binaries now include ZBar, and QR code scanning should work.
 * The Wallet Import Format (WIF) for private keys that was extended in 3.0
   is changed. Keys in the previous format can be imported, compatibility
   is maintained. Newly exported keys will be serialized as
   "script_type:original_wif_format_key".
 * BIP32 master keys for testnet once again have different version bytes than
   on mainnet. For the mainnet prefixes {x,y,Y,z,Z}|{pub,prv}, the
   corresponding testnet prefixes are   {t,u,U,v,V}|{pub,prv}.
   More details and exact version bytes are specified at:
   https://github.com/spesmilo/electrum-docs/blob/master/xpub_version_bytes.rst
   Note that due to this change, testnet wallet files created with previous
   versions of Electrum must be considered broken, and they need to be
   recreated from seed words.
 * A new version of the Electrum protocol is required by the client
   (version 1.2). Servers using older versions of the protocol will
   not be displayed in the GUI.


# Release 3.0.6 :
  * Fix transaction parsing bug #3788

# Release 3.0.5 : (Security update)

This is a follow-up to the 3.0.4 release, which did not completely fix
issue #3374. Users should upgrade to 3.0.5.

 * The JSONRPC interface is password protected
 * JSONRPC commands are disabled if the GUI is running, except 'ping',
   which is used to determine if a GUI is already running


# Release 3.0.4 : (Security update)

 * Fix a vulnerability caused by Cross-Origin Resource Sharing (CORS)
   in the JSONRPC interface. Previous versions of Electrum are
   vulnerable to port scanning and deanonimization attacks from
   malicious websites. Wallets that are not password-protected are
   vulnerable to theft.
 * Bundle QR scanner with Android app
 * Minor bug fixes

# Release 3.0.3
  * Qt GUI: sweeping now uses the Send tab, allowing fees to be set
  * Windows: if using the installer binary, there is now a separate shortcut
    for "Electrum Testnet"
  * Digital Bitbox: added support for p2sh-segwit
  * OS notifications for incoming transactions
  * better transaction size estimation:
    - fees for segwit txns were somewhat underestimated (#3347)
    - some multisig txns were underestimated
    - handle uncompressed pubkeys
  * fix #3321: testnet for Windows binaries
  * fix #3264: Ledger/dbb signing on some platforms
  * fix #3407: KeepKey sending to p2sh output
  * other minor fixes and usability improvements

# Release 3.0.2
  * Android: replace requests tab with address tab, with access to
    private keys
  * sweeping minikeys: search for both compressed and uncompressed
    pubkeys
  * fix wizard crash when attempting to reset Google Authenticator
  * fix #3248: fix Ledger+segwit signing
  * fix #3262: fix SSL payment request signing
  * other minor fixes.

# Release 3.0.1
  * minor bug and usability fixes

# Release 3.0 - Uncanny Valley (November 1st, 2017)

  * The project was migrated to Python3 and Qt5. Python2 is no longer
    supported. If you cloned the source repository, you will need to
    run "python3 setup.py install" in order to install the new
    dependencies.

  * Segwit support: 

    - Native segwit scripts are supported using a new type of
      seed. The version number for segwit seeds is 0x100. The install
      wizard will not create segwit seeds by default; users must
      opt-in with the segwit option.

    - Native segwit scripts are represented using bech32 addresses,
      following BIP173. Please note that BIP173 is still in draft
      status, and that other wallets/websites may not support
      it. Thus, you should keep a non-segwit wallet in order to be
      able to receive bitcoins during the transition period. If BIP173
      ends up being rejected or substantially modified, your wallet
      may have to be restored from seed. This will not affect funds
      sent to bech32 addresses, and it will not affect the capacity of
      Electrum to spend these funds.

    - Segwit scripts embedded in p2sh are supported with hardware
      wallets or bip39 seeds. To create a segwit-in-p2sh wallet,
      trezor/ledger users will need to enter a BIP49 derivation path.

    - The BIP32 master keys of segwit wallets are serialized using new
      version numbers. The new version numbers encode the script type,
      and they result in the following prefixes:

         * xpub/xprv : p2pkh or p2sh
         * ypub/yprv : p2wpkh-in-p2sh
         * Ypub/Yprv : p2wsh-in-p2sh
         * zpub/zprv : p2wpkh
         * Zpub/Zprv : p2wsh

      These values are identical for mainnet and testnet; tpub/tprv
      prefixes are no longer used in testnet wallets.

    - The Wallet Import Format (WIF) is similarly extended for segwit
      scripts. After a base58-encoded key is decoded to binary, its
      first byte encodes the script type:

         * 128 + 0: p2pkh
         * 128 + 1: p2wpkh
         * 128 + 2: p2wpkh-in-p2sh
         * 128 + 5: p2sh
         * 128 + 6: p2wsh
         * 128 + 7: p2wsh-in-p2sh

      The distinction between p2sh and p2pkh in private key means that
      it is not possible to import a p2sh private key and associate it
      to a p2pkh address.

  * A new version of the Electrum protocol is required by the client
    (version 1.1). Servers using older versions of the protocol will
    not be displayed in the GUI.

  * By default, transactions are time-locked to the height of the
    current block. Other values of locktime may be passed using the
    command line.


# Release 2.9.3
  * fix configuration file issue #2719
  * fix ledger signing of non-RBF transactions
  * disable 'spend confirmed only' option by default

# Release 2.9.2
  * force headers download if headers file is corrupted
  * add websocket to windows builds

# Release 2.9.1
  * fix initial headers download
  * validate contacts on import
  * command-line option for locktime

# Release 2.9 - Independence (July 27th, 2017)
  * Multiple Chain Validation: Electrum will download and validate
    block headers sent by servers that may follow different branches
    of a fork in the Bitcoin blockchain. Instead of a linear sequence,
    block headers are organized in a tree structure. Branching points
    are located efficiently using binary search. The purpose of MCV is
    to detect and handle blockchain forks that are invisible to the
    classical SPV model.
  * The desired branch of a blockchain fork can be selected using the
    network dialog. Branches are identified by the hash and height of
    the diverging block. Coin splitting is possible using RBF
    transaction (a tutorial will be added).
  * Multibit support: If the user enters a BIP39 seed (or uses a
    hardware wallet), the full derivation path is configurable in the
    install wizard.
  * Option to send only confirmed coins
  * Qt GUI:
    - Network dialog uses tabs and gets updated by network events.
    - The gui tabs use icons
  * Kivy GUI:
    - separation between network dialog and wallet settings dialog.
    - option for manual server entry
    - proxy configuration
  * Daemon: The wallet password can be passed as parameter to the
    JSONRPC API.
  * Various other bugfixes and improvements.


# Release 2.8.3
  * Fix crash on reading older wallet formats.
  * TrustedCoin: remove pay-per-tx option

# Release 2.8.2
  * show paid invoices in history tab
  * improve CPFP dialog
  * fixes for trezor, keepkey
  * other minor bugfixes

# Release 2.8.1
  * fix Digital Bitbox plugin
  * fix daemon jsonrpc
  * fix trustedcoin wallet creation
  * other minor bugfixes

# Release 2.8.0 (March 9, 2017)
  * Wallet file encryption using ECIES: A keypair is derived from the
    wallet password. Once the wallet is decrypted, only the public key
    is retained in memory, in order to save the encrypted file.
  * The daemon requires wallets to be explicitly loaded before
    commands can use them. Wallets can be loaded using: 'electrum
    daemon load_wallet [-w path]'. This command will require a
    password if the wallet is encrypted.
  * Invoices and contacts are stored in the wallet file and are no
    longer shared between wallets. Previously created invoices and
    contacts files may be imported from the menu.
  * Fees improvements:
    - Dynamic fees are enabled by default.
    - Child Pays For Parent (CPFP) dialog in the GUI.
    - RBF is automatically proposed for low fee transactions.
  * Support for Segregated Witness (testnet only).
  * Support for Digital Bitbox hardware wallet.
  * The GUI shows a blue icon when connected using a proxy.

# Release 2.7.18
  * enforce https on exchange rate APIs
  * use hardcoded list of exchanges
  * move 'Freeze' menu to Coins (utxo) tab
  * various bugfixes

# Release 2.7.17
  * fix a few minor regressions in the Qt GUI

# Release 2.7.16
  * add Testnet support (fix #541)
  * allow daemon to be launched in the foreground (fix #1873)
  * Qt: use separate tabs for addresses and UTXOs
  * Qt: update fee slider with a network callback
  * Ledger: new ui and mobile 2fa validation (neocogent)

# Release 2.7.15
  * Use fee slider for both static and dynamic fees.
  * Add fee slider to RBF dialog (fix #2083).
  * Simplify fee preferences.
  * Critical: Fix password update issue (#2097). This bug prevents
    password updates in multisig and 2FA wallets. It may also cause
    wallet corruption if the wallet contains several master private
    keys (such as 2FA wallets that have been restored from
    seed). Affected wallets will need to be restored again.

# Release 2.7.14
  * Merge exchange_rate plugin with main code
  * Faster synchronization and transaction creation
  * Fix bugs #2096, #2016

# Release 2.7.13
  * fix message signing with imported keys
  * add size to transaction details window
  * move plot plugin to main code
  * minor bugfixes

# Release 2.7.12
  various bugfixes

# Release 2.7.11
  * fix offline signing (issue #195)
  * fix android crashes caused by threads

# Release 2.7.10
  * various fixes for hardware wallets
  * improve fee bumping
  * separate sign and broadcast buttons in Qt tx dialog
  * allow spaces in private keys

# Release 2.7.9
  * Fix a bug with the ordering of pubkeys in recent multisig wallets.
    Affected wallets will regenerate their public keys when opened for
    the first time. This bug does not affect address generation.
  * Fix hardware wallet issues #1975, #1976

# Release 2.7.8
  * Fix a bug with fee bumping
  * Fix crash when parsing request (issue #1969)

# Release 2.7.7
  * Fix utf8 encoding bug with old wallet seeds (issue #1967)
  * Fix delete request from menu (issue #1968)

# Release 2.7.6
 * Fixes a critical bug with imported private keys (issue #1966). Keys
   imported in Electrum 2.7.x were not encrypted, even if the wallet
   had a password. If you imported private keys using Electrum 2.7.x,
   you will need to import those keys again. If you imported keys in
   2.6 and converted with 2.7.x, you don't need to do anything, but
   you still need to upgrade in order to be able to spend.
 * Wizard: Hide seed options in a popup dialog.

# Release 2.7.5
 * Add number of confirmations to request status. (issue #1757)
 * In the GUI, refer to passphrase as 'seed extension'.
 * Fix bug with utf8 encoded passphrases.
 * Kivy wizard: add a dialog for seed options.
 * Kivy wizard: add current word to suggestions, because some users
   don't see the space key.

# Release 2.7.4
 * Fix private key import in wizard
 * Fix Ledger display (issue #1961)
 * Fix old watching-only wallets (issue #1959)
 * Fix Android compatibility (issue #1947)

# Release 2.7.3
 * fix Trezor and Keepkey support in Windows builds
 * fix sweep private key dialog
 * minor fixes: #1958, #1959

# Release 2.7.2
 * fix bug in password update (issue #1954)
 * fix fee slider (issue #1953)

# Release 2.7.1
 * fix wizard crash with old seeds
 * fix issue #1948: fee slider

# Release 2.7.0 (Oct 2 2016)

 * The wallet file format has been upgraded. This upgrade is not
   backward compatible, which means that a wallet upgraded to the 2.7
   format will not be readable by earlier versions of
   Electrum. Multiple accounts inside the same wallet are not
   supported in the new format; the Qt GUI will propose to split any
   wallet that has several accounts. Make sure that you have saved
   your seed phrase before you upgrade Electrum.
 * This version introduces a separation between wallets types and
   keystores types. 'Wallet type' defines the type of Bitcoin contract
   used in the wallet, while 'keystore type' refers to the method used
   to store private keys. Therefore, so-called 'hardware wallets' will
   be referred to as 'hardware keystores'.
 * Hardware keystores:
   - The Ledger Nano S is supported.
   - Hardware keystores can be used as cosigners in multi-signature
     wallets.
   - Multiple hardware cosigners can be used in the same multisig
     wallet. One icon per keystore is displayed in the satus bar. Each
     connected device will co-sign the transaction.
 * Replace-By-Fee: RBF transactions are supported in both Qt and
   Android. A warning is displayed in the history for transactions
   that are replaceable, have unconfirmed parents, or that have very
   low fees.
 * Dynamic fees: Dynamic fees are enabled by default. A slider allows
   the user to select the expected confirmation time of their
   transaction. The expected confirmation times of incoming
   transactions is also displayed in the history.
 * The install wizards of Qt and Kivy have been unified.
 * Qt GUI (Desktop):
   - A fee slider is visible in the in send tab
   - The Address tab is hidden by default, can be shown with Ctrl-A
   - UTXOs are displayed in the Address tab
 * Kivy GUI (Android):
   - The GUI displays the complete transaction history.
   - Multisig wallets are supported.
   - Wallets can be created and deleted in the GUI.
 * Seed phrases can be extended with a user-chosen passphrase. The
   length of seed phrases is standardized to 12 words, using 132 bits
   of entropy (including 2FA seeds). In the wizard, the type of the
   seed is displayed in the seed input dialog.
 * TrustedCoin users can request a reset of their Google Authenticator
   account, if they still have their seed.


# Release 2.6.4 (bugfixes)
 * fix coinchooser bug (#1703)
 * fix daemon JSONRPC (#1731)
 * fix command-line broadcast (#1728)
 * QT: add colors to labels

# Release 2.6.3 (bugfixes)
 * fix command line parsing of transactions
 * fix signtransaction --privkey (#1715)

# Release 2.6.2 (bugfixes)
 * fix Trustedcoin restore from seed (bug #1704)
 * small improvements to kivy GUI

# Release 2.6.1 (bugfixes)
 * fix broadcast command (bug #1688)
 * fix tx dialog (bug #1690)
 * kivy: support old-type seed phrases in wizard

# Release 2.6
 * The source code is relicensed under the MIT Licence
 * First official release of the Kivy GUI, with android APK
 * The old 'android' and 'gtk' GUIs are deprecated
 * Separation between plugins and GUIs
 * The command line uses jsonrpc to communicate with the daemon
 * New command: 'notify <address> <url>'
 * Alternative coin selection policy, designed to help preserve user
   privacy. Enable it by setting the Coin Selection preference to
   Privacy.
 * The install wizard has been rewritten and improved
 * Support minikeys as used in Casascius coins for private key import
   and sweeping
 * Much improved support for TREZOR and KeepKey devices:
   - full device information display
   - initialize a new or wiped device in 4 ways:
     1) device generates a new wallet
     2) you enter a seed
     3) you enter a BIP39 mnemonic to generate the seed
     4) you enter a master private key
   - KeepKey secure seed recovery (KeepKey only)
   - change / set / disable PIN
   - set homescreen (TREZOR only)
   - set a session timeout.  Once a session has timed out, further use
     of the device requires your PIN and passhphrase to be re-entered
   - enable / disable passphrases
   - device wipe
   - multiple device support

# Release 2.5.4
 * increase MIN_RELAY_TX_FEE to avoid dust transactions

# Release 2.5.3 (bugfixes)
 * installwizard: do not allow direct copy-paste of the seed
 * installwizard: fix bug #1531 (starting offline)

# Release 2.5.2 (bugfixes)
 * fix bug #1513 (client tries to broadcast transaction while not connected)
 * fix synchronization bug (#1520)
 * fix command line bug (#1494)
 * fixes for exchange rate plugin

# Release 2.5.1 (bugfixes)
 * signatures in transactions were still using the old class
 * make sure that setup.py uses python2
 * fix wizard crash with trustedcoin plugin
 * fix socket infinite loop
 * fix history bug #1479

# Release 2.5
 * Low-S values are used in signatures (BIP 62).
 * The Kivy GUI has been merged into master.
 * The Qt GUI supports multiple windows in the same process. When a
   new Electrum instance is started, it checks for an already running
   Electrum process, and connects to it.
 * The network layer uses select(), so all server communication is
   handled by a single thread. Moreover, the synchronizer, verifier,
   and exchange rate plugin now run as separate jobs within the
   networking thread instead of as their own threads.
 * Plugins are revamped, particularly the exchange rate plugin.

# Release 2.4.4
 * Fix bug with TrustedCoin plugin

# Release 2.4.3
 * Support for KeepKey hardware wallet
 * Simplified Chinese wordlist
 * Minor bugfixes and GUI tweaks

# Release 2.4.2
 * Command line can read arguments from stdin (pipe)
 * Speedup fee computation for large transactions
 * Various bugfixes

# Release 2.4.1
 * Use ssl.PROTOCOL_TLSv1
 * Fix DNSSEC issues with ECDSA signatures
 * Replace TLSLite dependency with minimal RSA implementation
 * Dynamic Fees: using estimatefee value returned by server
 * Various GUI improvements

# Release 2.4
 * Payment to DNS names storing a Bitcoin addresses (OpenAlias) is
   supported directly, without activating a plugin. The verification
   uses DNSSEC.
 * The DNSSEC verification code was rewritten. The previous code,
   which was part of the OpenAlias plugin, is vulnerable and should
   not be trusted (Electrum 2.0 to 2.3).
 * Payment requests can be signed using Bitcoin addresses stored
   in DNS (OpenAlias). The identity of the requestor is verified using
   DNSSEC.
 * Payment requests signed with OpenAlias keys can be shared as
   bitcoin: URIs, if they are simple (a single address-type
   output). The BIP21 URI scheme is extended with 'name', 'sig',
   'time', 'exp'.
 * Arbitrary m-of-n multisig wallets are supported (n<=15).
 * Multisig transactions can be signed with TREZOR. When you create
   the multisig wallet, just enter the xpub of your existing TREZOR
   wallet.
 * Transaction fees set manually in the GUI are retained, including
   when the user uses the '!' shortcut.
 * New 'email' plugin, that enables sending and receiving payment
   requests by email.
 * The daemon supports Websocket notifications of payments.

# Release 2.3.3
 * fix proxy settings (issue #1309)
 * improvements to the transaction dialog:
    - request password after showing transaction
    - show change addresses in yellow color

# Release 2.3.2
 * minor bugfixes
 * updated ledger plugin
 * sort inputs/outputs lexicographically (BIP-LI01)

# Release 2.3.1
 * patch a bug with payment requests

# Release 2.3
 * Improved logic for the network layer.
 * More efficient coin selection. Spend oldest coins first, and
   minimize the number of transaction inputs.
 * Plugins are loaded independently of the GUI. As a result, Openalias,
   TrustedCoin and TREZOR wallets can be used with the command
   line. Example: 'electrum payto <openalias> <amount>'
 * The command line has been refactored:
  - Arguments are parsed with argparse.
  - The inline help includes a description of options.
  - Some commands have been renamed. Notably, 'mktx' and 'payto' have
    been merged into a single command, with a --broadcast option.
   Type 'electrum --help' for a complete overview.
 * The command line accepts the '!' syntax to send the maximum
   amount available. It can be combined with the '--from' option.
   Example: 'payto <destination> ! --from <from_address>'
 * The command line also accepts a '?' shortcut for private keys
   arguments, that triggers a prompt.
 * Payment requests can be managed with the command line, using the
   following commands: 'addrequest', 'rmrequest', 'listrequests'.
   Payment requests can be signed with a SSL certificate, and published
   as bip70 files in a public web directory. To see the relevant
   configuration variables, type 'electrum addrequest --help'
 * Commands can be called with jsonrpc, using the 'jsonrpc' gui. The
   jsonrpc interface may be called by php.

# Release 2.2
 * Show amounts (thousands separators and decimal point)
   according to locale in GUI
 * Show unmatured coins in balance
 * Fix exchange rates plugin
 * Network layer: refactoring and fixes

# Release 2.1.1
 * patch a bug that prevents new wallet creation.
 * fix connection issue on osx binaries

# Release 2.1
 * Faster startup, thanks to the following optimizations:
   1. Transaction input/outputs are cached in the wallet file
   2. Fast X509 certificate parser, not using pyasn1 anymore.
   3. The Label Sync plugin only requests modified labels.
 * The 'Invoices' and 'Send' tabs have been merged.
 * Contacts are stored in a separate file, shared between wallets.
 * A Search Box is available in the GUI (Ctrl-S)
 * Payment requests have an expiration date and can be exported to
   BIP70 files.
 * file: scheme support in BIP72 URIs: "bitcoin:?r=file:///..."
 * Own addresses are shown in green in the Transaction dialog.
 * Address History dialog.
 * The OpenAlias plugin was improved.
 * Various bug fixes and GUI improvements.
 * A new LabelSync backend is being used an import of the old
   database was made but since the release came later it's
   recommended that you do a full push when you upgrade.

# Release 2.0.4 - Minor GUI improvements
 * The password dialog will ask for password again if the user enters
   a wrong password
 * The Master Public Key dialog displays which keys belong to the
   wallet, and which are cosigners
 * The transaction dialog will ask to save unsaved transaction
   received from cosigner pool, when user clicks on 'Close'
 * The multisig restore dialog accepts xprv keys.
 * The network daemon must be started explicitly before using commands
   that require a connection
   Example:
     electrum daemon start
     electrum getaddressunspent <addr>
     electrum daemon status
     electrum daemon stop
   If a daemon is running, the GUI will use it.

# Release 2.0.3 - bugfixes and minor GUI improvements
 * Do not use daemon threads (fix #960)
 * Add a zoom button to receive tab
 * Add exchange rate conversion to receive tab
 * Use Tor's default port number in default proxy config

# Release 2.0.2 - bugfixes
 * Fix transaction sweep (#1066)
 * Fix thread timing bug (#1054)

# Release 2.0.1 - bugfixes
 * Fix critical bug in TREZOR address derivation: passphrases were not
   NFKD normalized. TREZOR users who created a wallet protected by a
   passphrase containing utf-8 characters with diacritics are
   affected. These users will have to open their wallet with version
   2.0 and to move their funds to a new wallet.
 * Use a file socket for the daemon (fixes network dialog issues)
 * Fix crash caused by QR scanner icon when zbar not installed.
 * Fix CosignerPool plugin
 * Label Sync plugin: Fix label sharing between multisig wallets


# Release 2.0

 * Before you upgrade, make sure you have saved your wallet seed on
   paper.

 * Documentation is now hosted on a wiki: http://electrum.orain.org

 * New seed derivation method (not compatible with BIP39). The seed
   phrase includes a version number, that refers to the wallet
   structure. The version number also serves as a checksum, and it
   will prevent the import of seeds from incompatible wallets. Old
   Electrum seeds are still supported.

 * New address derivation (BIP32). Standard wallets are single account
   and use a gap limit of 20.

 * Support for Multisig wallets using parallel BIP32 derivations and
   P2SH addresses ("2 of 2", "2 of 3").

 * Compact serialization format for unsigned or partially signed
   transactions, that includes the BIP32 master public key and
   derivation needed to sign inputs. Serialized transactions can be
   sent to cosigners or to cold storage using QR codes (using Andreas
   Schildbach's base 43 idea).

 * Support for BIP70 payment requests:
   - Verification of the chain of signatures uses tlslite.
   - In the GUI, payment requests are shown in the 'Invoices' tab.

 * Support for hardware wallets: TREZOR (SatoshiLabs) and Btchip (Ledger).

 * Two-factor authentication service by TrustedCoin. This service uses
   "2 of 3" multisig wallets and Google Authenticator. Note that
   wallets protected by this service can be deterministically restored
   from seed, without Trustedcoin's server.

 * Cosigner Pool plugin: encrypted communication channel for multisig
   wallets, to send and receive partially signed transactions.

 * Audio Modem plugin: send and receive transactions by sound.

 * OpenAlias plugin: send bitcoins to aliases verified using DNSSEC.

 * New 'Receive' tab in the GUI:
   - create and manage payment requests, with QR Codes
   - the former 'Receive' tab was renamed to 'Addresses'
   - the former Point of Sale plugin is replaced by a resizable
     window that pops up if you click on the QR code

 * The 'Send' tab in the Qt GUI supports transactions with multiple
   outputs, and raw hexadecimal scripts.

 * The GUI can connect to the Electrum daemon: "electrum -d" will
   start the daemon if it is not already running, and the GUI will
   connect to it. The daemon can serve several clients. It times out
   if no client uses if for more than 5 minutes.

 * The install wizard can be used to import addresses or private
   keys. A watching-only wallet is created by entering a list of
   addresses in the wizard dialog.

 * New file format: Wallets files are saved as JSON. Note that new
   wallet files cannot be read by older versions of Electrum. Old
   wallet files will be converted to the new format; this operation
   may take some time, because public keys will be derived for each
   address of your wallet.

 * The client accepts servers with a CA-signed SSL certificate.

 * ECIES encrypt/decrypt methods, available in the GUI and using
   the command line:
      encrypt <pubkey> <message>
      decrypt <pubkey> <message>

 * The Android GUI has received various updates and it is much more
   stable. Another script was added to Android, called Authenticator,
   that works completely offline: it reads an unsigned transaction
   shown as QR code, signs it and shows the result as a QR code.


# Release 1.9.8

* Electrum servers were upgraded to version 0.9. The new server stores
  a Patrica tree of all UTXOs, an idea proposed by Alan Reiner in the
  bitcointalk forum. This property allows the client to directly
  request the balance of any address. The new commands are:
     1. getaddressbalance <address>
     2. getaddressunspent <address>
     3. getutxoaddress <txid> <pos>

* Command-line commands that require a connection to the network spawn
  a daemon, that remains connected and handles subsequent
  commands. The daemon terminates itself if it remains unused for more
  than one minute. The purpose of this is to make scripting more
  efficient. For example, a bash script using many electrum commands
  will open only one connection.

# Release 1.9.7
* Fix for offline signing
* Various bugfixes
* GUI usability improvements
* Coinbase Buyback plugin

# Release 1.9.6
* During wallet creation, do not write seed to disk until it is encrypted.
* Confirmation dialog if the transaction fee is higher than 1mBTC.
* bugfixes

# Release 1.9.5

* Coin control: select addresses to send from
* Put addresses that have been used in a minimized section (Qt GUI)
* Allow non ascii chars in passwords


# Release 1.9.4
bugfixes: offline transactions

# Release 1.9.3
bugfixes: connection problems, transactions staying unverified

# Release 1.9.2
* fix a syntax error

# Release 1.9.1
* fix regression with --offline mode
* fix regression with --portable mode: use a dedicated directory

# Release 1.9

* The client connects to multiple servers in order to retrieve block headers and find the longest chain
* SSL certificate validation (to prevent MITM)
* Deterministic signatures (RFC 6979)
* Menu to create/restore/open wallets
* Create transactions with multiple outputs from CSV (comma separated values)
* New text gui: stdio
* Plugins are no longer tied to the qt GUI, they can reach all GUIs
* Proxy bugs have been fixed


# Release 1.8.1

* Notification option when receiving new transactions
* Confirm dialogue before sending large amounts
* Alternative datafile location for non-windows systems
* Fix offline wallet creation
* Remove enforced tx fee
* Tray icon improvements
* Various bugfixes


# Release 1.8

* Menubar in classic gui
* Updated the QR Code plugin to enable offline/online wallets to transmit unsigned/signed transactions via QR code.
* Fixed bug where never-confirmed transactions prevented further spending


# Release 1.7.4

* Increase default fee
* fix create and restore in command line
* fix verify message in the gui


# Release 1.7.3:

* Classic GUI can display amounts in mBTC
* Account selector in the classic GUI
* Changed the way the portable flag uses without supplying a -w argument
* Classic GUI asks users to enter their seed on wallet creation


# Release 1.7.2:

* Transactions that are in the same block are displayed in chronological order in the history.
* The client computes transaction priority and rejects zero-fee transactions that need a fee.
* The default fee was lowered to 200 uBTC per kb.
* Due to an internal format change, your history may be pruned when
  you open your wallet for the first time after upgrading to 1.7.2. If
  this is the case, please visit a full server to restore your full
  history. You will only need to do that once.


# Release 1.7.1:  bugfixes.


# Release 1.7

* The Classic GUI can be extended with plugins. Developers who want to
add new features or third-party services to Electrum are invited to
write plugins. Some previously existing and non-essential features of
Electrum (point-of-sale mode, qrcode scanner) were removed from the
core and are now available as plugins.

* The wallet waits for 2 confirmations before creating new
addresses. This makes recovery from seed more robust. Note that it
might create unwanted gaps if you use Electrum 1.7 together with older
versions of Electrum.

* An interactive Python console replaces the 'Wall' tab. The provided
python environment gives users access to the wallet and gui. Most
electrum commands are available as python function in the
console. Custom scripts an be loaded with a "run(filename)"
command. Tab-completions are available.

* The location of the Electrum folder in Windows changed from
LOCALAPPDATA to APPDATA. Discussion on this topic can be found here:
https://bitcointalk.org/index.php?topic=144575.0

* Private keys can be exported from within the classic GUI:
  For a single address, use the address menu (right-click).
  To export the keys of your entire wallet, use the settings dialog (import/export tab).

* It is possible to create, sign and redeem multisig transaction using the
command line interface.  This is made possible by the following new commands:
    dumpprivkey, listunspent, createmultisig, createrawtransaction, decoderawtransaction, signrawtransaction
The syntax of these commands is similar to their bitcoind counterpart.
For an example, see Gavin's tutorial: https://gist.github.com/gavinandresen/3966071

* Offline wallets now work in a way similar to Armory:
  1. user creates an unsigned transaction using the online (watching-only) wallet.
  2. unsigned transaction is copied to the offline computer, and signed by the offline wallet.
  3. signed transaction is copied to the online computer, broadcasted by the online client.
  4. All these steps can be done via the command line interface or the classic GUI.

* Many command line commands have been renamed in order to make the syntax consistent with bitcoind.

# Release 1.6.2

== Classic GUI
* Added new version notification

# Release 1.6.1 (11-01-2013)

== Core
* It is now possible to restore a wallet from MPK (this will create a watching-only wallet)
* A switch button allows to easily switch between Lite and Classic GUI.

== Classic GUI
* Seed and MPK help dialogs were rewritten
* Point of Sale: requested amounts can be expressed in other currencies and are converted to bitcoin.

== Lite GUI
* The receiving button was removed in favor of a menu item to keep it consistent with the history toggle.

# Release 1.6.0 (07-01-2013)

== Core
* (Feature) Add support for importing, signing and verifiying compressed keys
* (Feature) Auto reconnect to random server on disconnect
* (Feature) Ultimate fallback to HTTP port 80 if TCP doesn't work on any server
* (Bug) Under rare circumstances changing password with incorrect password could damage wallet

== Lite GUI
* (Chore) Use blockchain.info for exchange rate data
* (Feature) added currency conversion for BRL, CNY, RUB
* (Feature) Saraha theme
* (Feature) csv import/export for transactions including labels

== Classic GUI
* (Chore) pruning servers now called "p", full servers "f" to avoid confusion with terms
* (Feature) Debits in history shown in red
* (Feature) csv import/export for transactions including labels

# Release 1.5.8 (02-01-2013)

== Core
* (Bug) Fix pending address balance on received coins for pruning servers
* (Bug) Fix history command line option to show output again (regression by SPV)
* (Chore) Add timeout to blockchain headers file download by HTTP
* (Feature) new option: -L, --language: default language used in GUI.

== Lite GUI
* (Bug) Sending to auto-completed contacts works again
* (Chore) Added version number to title bar

== Classic GUI
* (Feature) Language selector in options.

# Release 1.5.7 (18-12-2012)

== Core
* The blockchain headers file is no longer included in the packages, it is downloaded on startup.
* New command line option: -P or --portable, for portable wallets. With this flag, all preferences are saved to the wallet file, and the blockchain headers file is in the same directory as the wallet

== Lite GUI
* (Feature) Added the ability to export your transactions to a CSV file.
* (Feature) Added a label dialog after sending a transaction.
* (Feature) Reworked receiving addresses; instead of a random selection from one of your receiving addresses a new widget will show listing unused addresses.
* (Chore)   Removed server selection. With all the new server options a simple menu item does not suffice anymore.
