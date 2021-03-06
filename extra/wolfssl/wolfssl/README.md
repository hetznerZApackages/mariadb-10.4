*** Description ***

The wolfSSL embedded SSL library (formerly CyaSSL) is a lightweight SSL/TLS
library written in ANSI C and targeted for embedded, RTOS, and
resource-constrained environments - primarily because of its small size, speed,
and feature set.  It is commonly used in standard operating environments as well
because of its royalty-free pricing and excellent cross platform support.
wolfSSL supports industry standards up to the current TLS 1.3 and DTLS 1.2
levels, is up to 20 times smaller than OpenSSL, and offers progressive ciphers
such as ChaCha20, Curve25519, NTRU, and Blake2b. User benchmarking and feedback
reports dramatically better performance when using wolfSSL over OpenSSL.

wolfSSL is powered by the wolfCrypt library. Two versions of the wolfCrypt
cryptography library have been FIPS 140-2 validated (Certificate #2425 and
certificate #3389). For additional information, visit the wolfCrypt FIPS FAQ
(https://www.wolfssl.com/license/fips/) or contact fips@wolfssl.com

*** Why choose wolfSSL? ***

There are many reasons to choose wolfSSL as your embedded SSL solution. Some of
the top reasons include size (typical footprint sizes range from 20-100 kB),
support for the newest standards (SSL 3.0, TLS 1.0, TLS 1.1, TLS 1.2, TLS 1.3,
DTLS 1.0, and DTLS 1.2), current and progressive cipher support (including
stream ciphers), multi-platform, royalty free, and an OpenSSL compatibility API
to ease porting into existing applications which have previously used the
OpenSSL package. For a complete feature list, see chapter 4 of the wolfSSL
manual. (https://www.wolfssl.com/docs/wolfssl-manual/ch4/)

*** Notes, Please read ***

Note 1)
wolfSSL as of 3.6.6 no longer enables SSLv3 by default.  wolfSSL also no longer
supports static key cipher suites with PSK, RSA, or ECDH. This means if you
plan to use TLS cipher suites you must enable DH (DH is on by default), or
enable ECC (ECC is on by default), or you must enable static key cipher suites
with

    WOLFSSL_STATIC_DH
    WOLFSSL_STATIC_RSA
      or
    WOLFSSL_STATIC_PSK

though static key cipher suites are deprecated and will be removed from future
versions of TLS.  They also lower your security by removing PFS.  Since current
NTRU suites available do not use ephemeral keys, WOLFSSL_STATIC_RSA needs to be
used in order to build with NTRU suites.

When compiling ssl.c, wolfSSL will now issue a compiler error if no cipher
suites are available. You can remove this error by defining
WOLFSSL_ALLOW_NO_SUITES in the event that you desire that, i.e., you're not
using TLS cipher suites.

Note 2)
wolfSSL takes a different approach to certificate verification than OpenSSL
does. The default policy for the client is to verify the server, this means
that if you don't load CAs to verify the server you'll get a connect error,
no signer error to confirm failure (-188).

If you want to mimic OpenSSL behavior of having SSL_connect succeed even if
verifying the server fails and reducing security you can do this by calling:

    wolfSSL_CTX_set_verify(ctx, SSL_VERIFY_NONE, 0);

before calling wolfSSL_new();. Though it's not recommended.

Note 3)
The enum values SHA, SHA256, SHA384, SHA512 are no longer available when
wolfSSL is built with --enable-opensslextra (OPENSSL_EXTRA) or with the macro
NO_OLD_SHA_NAMES. These names get mapped to the OpenSSL API for a single call
hash function. Instead the name WC_SHA, WC_SHA256, WC_SHA384 and WC_SHA512
should be used for the enum name.

*** end Notes ***


# wolfSSL Release 4.6.0 (December 22, 2020)
Release 4.6.0 of wolfSSL embedded TLS has bug fixes and new features including:

### New Feature Additions
###### New Build Options
* wolfSSL now enables linux kernel module support. Big news for Linux kernel module developers with crypto requirements! wolfCrypt and wolfSSL are now loadable as modules in the Linux kernel, providing the entire libwolfssl API natively to other kernel modules. For the first time on Linux, the entire TLS protocol stack can be loaded as a module, allowing fully kernel-resident TLS/DTLS endpoints with in-kernel handshaking.  (--enable-linuxkm, --enable-linuxkm-defaults, --with-linux-source) (https://www.wolfssl.com/loading-wolfssl-into-the-linux-kernel/)
* Build tests and updated instructions for use with Apple???s A12Z chipset  (https://www.wolfssl.com/preliminary-cryptographic-benchmarks-on-new-apple-a12z-bionic-platform/)
* Expansion of wolfSSL SP math implementation and addition of --enable-sp-math-all build option
* Apache httpd w/TLS 1.3 support added
* Sniffer support for TLS 1.3 and AES CCM
* Support small memory footprint build with only TLS 1.3 and PSK without code for (EC)DHE and certificates

###### New Hardware Acceleration
* Added support for NXP DCP (i.MX RT1060/1062) crypto co-processor
* Add Silicon Labs hardware acceleration using [SL SE Manager](https://docs.silabs.com/gecko-platform/latest/service/api/group-sl-se-manager)

###### New Algorithms
* RC2 ECB/CBC added for use with PKCS#12 bundles
* XChaCha and the XChaCha20-Poly1305 AEAD algorithm support added

###### Misc
* Added support for 802.11Q VLAN frames to sniffer
* Added OCSP function wolfSSL_get_ocsp_producedDate
* Added API to set CPU ID flags cpuid_select_flags, cpuid_set_flag, cpuid_clear_flag
* New DTLS/TLS non-blocking Secure Renegotiation example added to server.c and client.c

### Fixes
###### Math Library
* Fix mp_to_unsigned_bin_len out of bounds read with buffers longer than maximum MP
* Fix for fp_read_radix_16 out of bounds read
* Fix to add wrapper for new timing resistant wc_ecc_mulmod_ex2 function version in HW ECC acceleration
* Handle an edge case with RSA-PSS encoding message to hash

###### Compatibility Layer Fixes
* Fix for setting serial number wolfSSL_X509_set_serialNumber
* Fix for setting ASN1 time not before / not after with WOLFSSL_X509
* Fix for order of components in issuer name when using X509_sign
* Fix for compatibility layer API DH_compute_key
* EVP fix incorrect block size for GCM and buffer up AAD for encryption/decryption
* EVP fix for AES-XTS key length return value and fix for string compare calls
* Fix for mutex freeing during RNG failure case with EVP_KEY creation
* Non blocking use with compatibility layer BIOs in TLS connections

###### Build Configuration
* Fix for custom build with WOLFSSL_USER_MALLOC defined
* ED448 compiler warning on Intel 32bit systems
* CURVE448_SMALL build fix for 32bit systems with Curve448
* Fix to build SP math with IAR
* CMake fix to only set ranlib arguments for Mac, and for stray typo of , -> ;
* Build with --enable-wpas=small fix
* Fix for building fips ready using openssl extra
* Fixes for building with Microchip (min/max and undef SHA_BLOCK_SIZE)
* FIx for NO_FILESYSTEM build on Windows
* Fixed SHA256 support for IMX-RT1060
* Fix for ECC key gen with NO_TFM_64BIT

###### Sniffer
* Fixes for sniffer when using static ECC keys. Adds back TLS v1.2 static ECC key fallback detection and fixes new ECC RNG requirement for timing resistance
* Fix for sniffer with SNI enabled to properly handle WOLFSSL_SUCCESS error code in ProcessClientHello
* Fix for sniffer using HAVE_MAX_FRAGMENT in "certificate" type message
* Fix build error with unused "ret" when building with WOLFSSL_SNIFFER_WATCH.
* Fix to not treat cert/key not found as error in myWatchCb and WOLFSSL_SNIFFER_WATCH.
* Sniffer fixes for handling TCP `out-of-range sequence number`
* Fixes SSLv3 use of ECDH in sniffer

###### PKCS
* PKCS#11 fix to generate ECC key for decrypt/sign or derive
* Fix for resetting internal variables when parsing a malformed PKCS#7 bundle with PKCS7_VerifySignedData()
* Verify the extracted public key in wc_PKCS7_InitWithCert
* Fix for internal buffer size when using decompression with PKCS#7

###### Misc
* Pin the C# verify callback function to keep from garbage collection
* DH fixes for when public key is owned and free???d after a handshake
* Fix for TLS 1.3 early data packets
* Fix for STM32 issue with some Cube HAL versions and STM32 example timeout
* Fix mmCAU and LTC hardware mutex locking to prevent double lock
* Fix potential race condition with CRL monitor
* Fix for possible malformed encrypted key with 3DES causing negative length
* AES-CTR performance fixed with AES-NI

### Improvements/Optimizations
##### SP and Math
* mp_radix_size adjustment for leading 0
* Resolve implicit cast warnings with SP build
* Change mp_sqr to return an error if the result won't fit into the fixed length dp
* ARM64 assembly with clang improvements, clang doesn't always handle use of x29 (FP or Frame Pointer) in inline assembly code correctly - reworked sp_2048_sqr_8 to not use x29
* SP mod exp changed to support exponents of different lengths
* TFM div: fix initial value of size in q so clamping doesn't OOB read
* Numerous stack depth improvements with --enable-smallstack
* Improve cache resistance with Base64 operations

###### TLS 1.3
* TLS 1.3 wolfSSL_peek want read return addition
* TLS 1.3: Fix P-521 algorithm matching

###### PKCS
* Improvements and refactoring to PKCS#11 key look up
* PKCS #11 changes for signing and loading RSA public key from private
* check PKCS#7 SignedData private key is valid before using it
* check PKCS#7 VerifySignedData content length against total bundle size to avoid large malloc

###### Compatibility Layer
* EVP add block size for more ciphers in wolfSSL_EVP_CIPHER_block_size()
* Return long names instead of short names in wolfSSL_OBJ_obj2txt()
* Add additional OpenSSL compatibility functions to update the version of Apache httpd supported
* add "CCM8" variants to cipher_names "CCM-8" ciphers, for OpenSSL compat

###### Builds
* Cortex-M SP ASM support for IAR 6.70
* STM Cube pack support (IDE/STM32Cube)
* Build option --enable-aesgcm=4bit added for AES-GCM GMULT using 4 bit table
* Xilinx IDE updates to allow XTIME override for Xilinx, spelling fixes in Xilinx README.md, and add Xilinx SDK printf support
* Added ED448 to the "all" options and ED448 check key null argument sanity check
* Added ARC4, 3DES, nullcipher, BLAKE2, BLAKE2s, XChaCha, MD2, and MD4 to the ???all??? options
* Added an --enable-all-crypto option, to enable only the wolfCrypt features of --enable-all, combinable with --enable-cryptonly
* Added the ability to selectively remove features from --enable-all and --enable-all-crypto using specific --disable-<feature> options
* Use Intel intrinsics with Windows for RDSEED and RDRAND (thanks to dr-m from MariaDB)
* Add option to build with WOLFSSL_NO_CLIENT_AUTH
* Updated build requirements for wolfSSH use to be less restrictive
* lighttpd support update for v1.4.56
* Added batch file to copy files to ESP-IDF folders and resolved warnings when using v4.0 ESP-IDF
* Added --enable-stacksize=verbose, showing at a glance the stack high water mark for each subtest in testwolfcrypt

###### ECC
* Performance increase for ECC verify only, using non constant time SP modinv
* During ECC verify add validation of r and s before any use
* Always use safe add and dbl with ECC
* Timing resistant scalar multiplication updated with use of Joye double-add ladder
* Update mp_jacobi function to reduce stack and increase performance for base ECC build
* Reduce heap memory use with wc_EccPrivateKeyDecode, Improvement to ECC wc_ecc_sig_to_rs and wc_ecc_rs_raw_to_sig to reduce memory use (avoid the mp_int)
* Improve StoreECC_DSA_Sig bounds checking

###### OCSP
* OCSP improvement to handle extensions in singleResponse
* support for OCSP request/response for multiple certificates
* OCSP Must Staple option added to require OCSP stapling response
* Add support for id-pkix-ocsp-nocheck extension

###### Misc
* Additional code coverage added for ECC and RSA, PKCS#7, 3DES, EVP and Blake2b operations
* DTLS MTU: check MTU on write
* Refactor hash sig selection and add the macros WOLFSSL_STRONGEST_HASH_SIG (picks the strongest hash) and WOLFSSL_ECDSA_MATCH_HASH (will pick the hash to match the ECC curve)
* Strict certificate version allowed from client, TLS 1.2 / 1.3 can not accept client certificates lower than version 3
* wolfSSL_get_ciphers_compat(), skip the fake indicator ciphers like the renegotiation indication and the quantum-safe hybrid
* When parsing session ticket, check TLS version to see whether they are version compatible
* Additional sanity check for invalid ASN1 padding on integer type
* Adding in ChaCha20 streaming feature with Mac and Intel assembly build
* Sniffer build with --enable-oldtls option on


For additional vulnerability information visit the vulnerability page at
https://www.wolfssl.com/docs/security-vulnerabilities/

See INSTALL file for build instructions.
More info can be found on-line at https://wolfssl.com/wolfSSL/Docs.html



*** Resources ***


[wolfSSL Website](https://www.wolfssl.com/)

[wolfSSL Wiki](https://github.com/wolfSSL/wolfssl/wiki)

[FIPS FAQ](https://wolfssl.com/license/fips)

[wolfSSL Documents](https://wolfssl.com/wolfSSL/Docs.html)

[wolfSSL Manual](https://wolfssl.com/wolfSSL/Docs-wolfssl-manual-toc.html)

[wolfSSL API Reference]
(https://wolfssl.com/wolfSSL/Docs-wolfssl-manual-17-wolfssl-api-reference.html)

[wolfCrypt API Reference]
(https://wolfssl.com/wolfSSL/Docs-wolfssl-manual-18-wolfcrypt-api-reference.html)

[TLS 1.3](https://www.wolfssl.com/docs/tls13/)

[wolfSSL Vulnerabilities]
(https://www.wolfssl.com/docs/security-vulnerabilities/)
