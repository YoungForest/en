---
title: Hennge challenge 3
date: 2022-03-19 13:17:20
tags:
- Hennge
categories:
- interview
---

Last Sunday, I submitted my application for the Hennge Back-end(Tokyo, Japan) SDE position. After that, my mail box received a Challenge 3 email from Hennge. The challenge is very interesting. When I had enough free time on this Saturday, I finished this challenge finally. I spent about 3 hours on this. The process is here.

## Challenge details

The Challenge link is 403 after I submitted my solution. So, I share the process from my memory.

First, implement a sum of squares of numbers program with Python/Go. You need to implement the input and output process by yourself.
Second, create a private github gist with the code.
Last, post the gist link to the Hennge server.

The code is very simple. I used Python to implement it.

```python
from functools import reduce

def main():
    N = int(input())
    ans = []
    for _ in range(N):
        X = int(input())
        ans.append(reduce(lambda x, y: x + (y * y if y > 0 else 0), map(int, input().split()), 0))
    print(*ans, sep='\n')

if __name__ == "__main__":
    main()
```

After struggle with Github two-factor authentication and manual account recovery review, I finally sign in to Github and created [my gist](https://gist.github.com/YoungForest/329c22200e7fad36e6ef984e3afe0325).

I think the hardest part is the authentication POST.

First, I needed to covert the shared secret to TOTP (Time-Based One-Time Password) with RFC 6238. Fortunately, I found a sample Java implementation in [the RFC 6238 document](https://datatracker.ietf.org/doc/html/rfc6238). By the way, because the input in the code is a HEX string. I covert my shared secret `sen.yang96@outlook.comHENNGECHALLENGE003` to HEX string with [a online tool](https://www.convertstring.com/EncodeDecode/HexEncode). The challenge required a 10-digits code. The default in the code is 8-digits. I met this response using 8-digits.
```json
{
     "message": "Access denied: Invalid token, malformed token"
}
```

After I changed it to 10-digits, the error updated to:
```json
{
     "message": "Access denied: Invalid token, wrong code"
}
```

I debugged a lot and found that it is the issue of timestamp. Because TOTP is time-based.
When I change the timestamp to `Instant.now() + 30s`, the password passed:
```json
{"message":"Congratulations! You have achieved mission 3"}
```

The final code after my changes is:

```java
/**
Copyright (c) 2011 IETF Trust and the persons identified as
authors of the code. All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, is permitted pursuant to, and subject to the license
terms contained in, the Simplified BSD License set forth in Section
4.c of the IETF Trust's Legal Provisions Relating to IETF Documents
(http://trustee.ietf.org/license-info).
*/

import java.lang.reflect.UndeclaredThrowableException;
import java.security.GeneralSecurityException;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.math.BigInteger;
import java.util.TimeZone;
import java.time.Instant;

/**
 * This is an example implementation of the OATH
 * TOTP algorithm.
 * Visit www.openauthentication.org for more information.
 *
 * @author Johan Rydell, PortWise, Inc.
 */

public class TOTP {

    private TOTP() {
    }

    /**
     * This method uses the JCE to provide the crypto algorithm.
     * HMAC computes a Hashed Message Authentication Code with the
     * crypto hash algorithm as a parameter.
     *
     * @param crypto:   the crypto algorithm (HmacSHA1, HmacSHA256,
     *                  HmacSHA512)
     * @param keyBytes: the bytes to use for the HMAC key
     * @param text:     the message or text to be authenticated
     */
    private static byte[] hmac_sha(String crypto, byte[] keyBytes,
            byte[] text) {
        try {
            Mac hmac;
            hmac = Mac.getInstance(crypto);
            SecretKeySpec macKey = new SecretKeySpec(keyBytes, "RAW");
            hmac.init(macKey);
            return hmac.doFinal(text);
        } catch (GeneralSecurityException gse) {
            throw new UndeclaredThrowableException(gse);
        }
    }

    /**
     * This method converts a HEX string to Byte[]
     *
     * @param hex: the HEX string
     *
     * @return: a byte array
     */

    private static byte[] hexStr2Bytes(String hex) {
        // Adding one byte to get the right conversion
        // Values starting with "0" can be converted
        byte[] bArray = new BigInteger("10" + hex, 16).toByteArray();

        // Copy all the REAL bytes, not the "first"
        byte[] ret = new byte[bArray.length - 1];
        for (int i = 0; i < ret.length; i++)
            ret[i] = bArray[i + 1];
        return ret;
    }

    private static final int[] DIGITS_POWER
    // 0 1 2 3 4 5 6 7 8 9 10
            = { 1, 10, 100, 1000, 10000, 100000, 1000000, 10000000, 100000000, 1000000000, 1000000000 };

    /**
     * This method generates a TOTP value for the given
     * set of parameters.
     *
     * @param key:          the shared secret, HEX encoded
     * @param time:         a value that reflects a time
     * @param returnDigits: number of digits to return
     *
     * @return: a numeric String in base 10 that includes
     *          {@link truncationDigits} digits
     */

    public static String generateTOTP(String key,
            String time,
            String returnDigits) {
        return generateTOTP(key, time, returnDigits, "HmacSHA1");
    }

    /**
     * This method generates a TOTP value for the given
     * set of parameters.
     *
     * @param key:          the shared secret, HEX encoded
     * @param time:         a value that reflects a time
     * @param returnDigits: number of digits to return
     *
     * @return: a numeric String in base 10 that includes
     *          {@link truncationDigits} digits
     */

    public static String generateTOTP256(String key,
            String time,
            String returnDigits) {
        return generateTOTP(key, time, returnDigits, "HmacSHA256");
    }

    /**
     * This method generates a TOTP value for the given
     * set of parameters.
     *
     * @param key:          the shared secret, HEX encoded
     * @param time:         a value that reflects a time
     * @param returnDigits: number of digits to return
     *
     * @return: a numeric String in base 10 that includes
     *          {@link truncationDigits} digits
     */

    public static String generateTOTP512(String key,
            String time,
            String returnDigits) {
        return generateTOTP(key, time, returnDigits, "HmacSHA512");
    }

    /**
     * This method generates a TOTP value for the given
     * set of parameters.
     *
     * @param key:          the shared secret, HEX encoded
     * @param time:         a value that reflects a time
     * @param returnDigits: number of digits to return
     * @param crypto:       the crypto function to use
     *
     * @return: a numeric String in base 10 that includes
     *          {@link truncationDigits} digits
     */

    public static String generateTOTP(String key,
            String time,
            String returnDigits,
            String crypto) {
        int codeDigits = Integer.decode(returnDigits).intValue();
        String result = null;

        // Using the counter
        // First 8 bytes are for the movingFactor
        // Compliant with base RFC 4226 (HOTP)
        while (time.length() < 16)
            time = "0" + time;

        // Get the HEX in a Byte[]
        byte[] msg = hexStr2Bytes(time);
        byte[] k = hexStr2Bytes(key);
        byte[] hash = hmac_sha(crypto, k, msg);

        // put selected bytes into result int
        int offset = hash[hash.length - 1] & 0xf;

        int binary = ((hash[offset] & 0x7f) << 24) |
                ((hash[offset + 1] & 0xff) << 16) |
                ((hash[offset + 2] & 0xff) << 8) |
                (hash[offset + 3] & 0xff);

        int otp = binary % DIGITS_POWER[codeDigits];

        result = Integer.toString(otp);
        while (result.length() < codeDigits) {
            result = "0" + result;
        }
        return result;
    }

    public static void main(String[] args) {
        // Seed for HMAC-SHA1 - 20 bytes
        String seed = "3132333435363738393031323334353637383930";
        // Seed for HMAC-SHA256 - 32 bytes
        String seed32 = "3132333435363738393031323334353637383930" +
                "313233343536373839303132";
        // Seed for HMAC-SHA512 - 64 bytes
        String seed64 = "73656E2E79616E673936406F75746C6F6F6B2E636F6D48454E4E47454348414C4C454E4745303033";
        // "3132333435363738393031323334353637383930" +
        //         "3132333435363738393031323334353637383930" +
        //         "3132333435363738393031323334353637383930" +
        //         "31323334";
        long T0 = 0;
        long X = 30;
        long testTime[] = { Instant.now().toEpochMilli() / 1000L + 30 }; // { 30L, 59L, 1111111109L, 1111111111L,
                // 1234567890L, 2000000000L, 20000000000L };

        String steps = "0";
        DateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        df.setTimeZone(TimeZone.getTimeZone("UTC"));

        try {
            System.out.println(
                    "+---------------+-----------------------+" +
                            "------------------+--------+--------+");
            System.out.println(
                    "|  Time(sec)    |   Time (UTC format)   " +
                            "| Value of T(Hex)  |  TOTP  | Mode   |");
            System.out.println(
                    "+---------------+-----------------------+" +
                            "------------------+--------+--------+");

            for (int i = 0; i < testTime.length; i++) {
                long T = (testTime[i] - T0) / X;
                steps = Long.toHexString(T).toUpperCase();
                while (steps.length() < 16)
                    steps = "0" + steps;
                String fmtTime = String.format("%1$-11s", testTime[i]);
                String utcTime = df.format(new Date(testTime[i] * 1000));
                System.out.print("|  " + fmtTime + "  |  " + utcTime +
                        "  | " + steps + " |");
                System.out.println(generateTOTP(seed, steps, "8",
                        "HmacSHA1") + "| SHA1   |");
                System.out.print("|  " + fmtTime + "  |  " + utcTime +
                        "  | " + steps + " |");
                System.out.println(generateTOTP(seed32, steps, "8",
                        "HmacSHA256") + "| SHA256 |");
                System.out.print("|  " + fmtTime + "  |  " + utcTime +
                        "  | " + steps + " |");
                System.out.println(generateTOTP(seed64, steps, "10",
                        "HmacSHA512") + "| SHA512 |");

                System.out.println(
                        "+---------------+-----------------------+" +
                                "------------------+--------+--------+");
            }
        } catch (final Exception e) {
            e.printStackTrace();
        }
    }
}
```

Second, I should send my post with HTTP Basic Authentication (RFC 2617).
The use-id is my email and password is the TOTP code. With the help of [this StackOverflow question: basic authorization command for curl](https://stackoverflow.com/questions/20803339/basic-authorization-command-for-curl), there is 2 options to do this.

1. Manually get Base64 encoded string and send it.

```shell
# I tested on a Mac OS.
echo -ne "sen.yang96@outlook.com:<TOTP 10 digits code>" | base64
# output is something like this: c2VuLnlhbmc5NkBvdXRsb29rLmNvbTo4MzUzMDkyMA==
curl -d '{  "github_url": "https://gist.github.com/YoungForest/329c22200e7fad36e6ef984e3afe0325",  "contact_email": "sen.yang96@outlook.com",  "solution_language": "python"}' -H 'Content-Type: application/json' -H 'Authorization: Basic <Base64 string>' https://api.challenge.hennge.com/challenges/003
```

2. CURL could help you do the Base64 encoding.
```shell
curl -d '{  "github_url": "https://gist.github.com/YoungForest/329c22200e7fad36e6ef984e3afe0325",  "contact_email": "sen.yang96@outlook.com",  "solution_language": "python"}' -H 'Content-Type: application/json' -u sen.yang96@outlook.com:<TOTP 10 digits code> https://api.challenge.hennge.com/challenges/003
```

## Follow up

After the congratulations response, I received a link by email to submit my Resume and CV.
It was my first time to do the challenge in a job application. I had done some homeworks/assignments and OA tests before. This challenge is so different and interesting. Through it, I learned a lot about TOTP and HTTP authentication.