---
categories:
- Device Tips
date: "2019-05-22"
layout: post
tags:
- microcontroller
- programming
- cortex-m
- benchmark
title: Handshake Times by Cipher using mbedTLS
chart: true
---

I ran some mbedTLS handshakes with various ciphers to see relative performance on an STM32F746 (Nucleo 144 board) running [Stratify OS](https://docs.stratifyos.co) at 216MHz. These are the results I got.

## A Few Notes

- The hardware uses ethernet and connects to a router.
- The test connected to Google's Firebase (real-time database) and updated one value which consisted of a couple hundred bytes of data.
- I only tested ciphers that I thought would work. Firebase doesn't support every possible cipher so in many cases the handshake failed.
- The tests were run with debugging on. So the values don't represent optimal times but they can be compared for relative performance.

## The Results

```chart
    {
    "type": "bar",
        "data": {
            "labels": ["RSA/AES128CBC/SHA", 
                "RSA/AES256CBC/SHA", 
                "RSA/AES128CBC/SHA256", 
                "RSA/AES256CBC/SHA256",
                "RSA/AES128GCM/SHA256",
                "RSA/AES256GCM/SHA384",
                "EDCHE/RSA/AES128CBC/SHA", 
                "EDCHE/RSA/AES256CBC/SHA", 
                "EDCHE/RSA/AES128CBC/SHA256", 
                "EDCHE/RSA/AES256CBC/SHA256",
                "EDCHE/RSA/AES128GCM/SHA256",
                "EDCHE/RSA/AES256GCM/SHA384"
                ],
            "datasets": [
            {
                "label": "Handshake in milliseconds",
                "data": [
                    1251,
                    1217,
                    1328,
                    1226,
                    1249,
                    1334,
                    4921,
                    4986,
                    4891,
                    4856,
                    5006,
                    4974
                ]
            }

            ]
        },
        "options": {
            "legend": {
                "show": "false"
            },
            "scales": {
                "xAxes" : [{
                    "ticks": {
                        "autoSkip": false
                    }
                }]

            }
        }
    }
```

Cipher | Execution Time
-------|--------------------
`MBEDTLS_TLS_RSA_WITH_AES_128_CBC_SHA` | 1251ms
`MBEDTLS_TLS_RSA_WITH_AES_256_CBC_SHA` | 1217ms
`MBEDTLS_TLS_RSA_WITH_AES_128_CBC_SHA256` | 1328ms
`MBEDTLS_TLS_RSA_WITH_AES_256_CBC_SHA256` | 1226ms
`MBEDTLS_TLS_RSA_WITH_AES_128_GCM_SHA256` | 1249ms
`MBEDTLS_TLS_RSA_WITH_AES_256_GCM_SHA384` | 1334ms
`MBEDTLS_TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA` | 4921ms
`MBEDTLS_TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA` | 4986ms
`MBEDTLS_TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` | 4891ms
`MBEDTLS_TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384` | 4856ms
`MBEDTLS_TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` | 5006ms
`MBEDTLS_TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384` | 4974ms

> With debugging mostly disabled `MBEDTLS_TLS_RSA_WITH_AES_128_GCM_SHA256` improved to 761ms from 1249ms.

You can see a clear pattern in the results. `RSA` only was much faster than `ECDHE_RSA`. `ECDHE_RSA` uses two ciphers--one for the server certificate and one for the key exchange--while `RSA` just uses one. Because the data exchange was so small, the performance differences for the AES portion doesn't really show up in the results.

The following ciphers failed (meaning incompatibility with Firebase).

- `MBEDTLS_TLS_RSA_WITH_NULL_MD5`
- `MBEDTLS_TLS_DHE_RSA_WITH_AES_128_CBC_SHA`
- `MBEDTLS_TLS_DHE_RSA_WITH_AES_256_CBC_SHA`
- `MBEDTLS_TLS_RSA_WITH_NULL_SHA256`
- `MBEDTLS_TLS_RSA_WITH_CAMELLIA_128_CBC_SHA`
- `MBEDTLS_TLS_PSK_WITH_AES_128_CBC_SHA`
- `MBEDTLS_TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
- `MBEDTLS_TLS_PSK_WITH_AES_128_GCM_SHA256`
- `MBEDTLS_TLS_RSA_PSK_WITH_AES_128_GCM_SHA256`
- `MBEDTLS_TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA`
- `MBEDTLS_TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
- `MBEDTLS_TLS_ECDH_RSA_WITH_AES_128_CBC_SHA`
- `MBEDTLS_TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
- `MBEDTLS_TLS_ECDH_RSA_WITH_AES_128_CBC_SHA256`
- `MBEDTLS_TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `MBEDTLS_TLS_ECDH_RSA_WITH_AES_128_GCM_SHA256`
- `MBEDTLS_TLS_ECDHE_PSK_WITH_RC4_128_SHA`
- `MBEDTLS_TLS_RSA_WITH_ARIA_128_CBC_SHA256`
- `MBEDTLS_TLS_RSA_WITH_AES_128_CCM`



