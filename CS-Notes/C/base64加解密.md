## 头文件

```c
#include <ctype.h>
#include <string.h>
```

## 宏定义

```c

#define BAD -1

#define INVALID_ARG -1
#define WRONG_FORMAT -2
#define OUTPUT_OVERFLOW -3

static const char base64digits[] = // characters used for base64 code
    "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";

static const char base64val[] = { // base64 alphabet
    BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD,
    BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD,
    BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, BAD, 62, BAD, BAD, BAD, 63,
    52, 53, 54, 55, 56, 57, 58, 59, 60, 61, BAD, BAD, BAD, BAD, BAD, BAD,
    BAD, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14,
    15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, BAD, BAD, BAD, BAD, BAD,
    BAD, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40,
    41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, BAD, BAD, BAD, BAD, BAD};

#define ENCODE64(c) (((c)&0xC0) ? BAD : base64digits[(int)c])
#define DECODE64(c) (((c)&0x80) ? BAD : base64val[(int)c])
```

## 加密

```c
int base64_encode(char *out, const unsigned char *in, int inlen, int maxlen)
{
    int outlen = 0;
    char *out_org = out;
    memset(out, 0x00, maxlen);

    // check if arguments valid
    if (!out || !in)
    {
        return INVALID_ARG;
    }

    // encode each three ascii characters
    for (; inlen >= 3; inlen -= 3, in += 3)
    {
        // update output length and check overflow
        outlen += 4;
        if (outlen >= maxlen && maxlen)
        {
            // use >= because there must have a '\0'
            *out_org = '\0';
            return OUTPUT_OVERFLOW;
        }
        if ((*out++ = ENCODE64((in[0] >> 2) & 0x3F)) == BAD)
        {
            *out_org = '\0';
            return WRONG_FORMAT;
        }
        if ((*out++ = ENCODE64(((in[0] << 4) & 0x30) | ((in[1] >> 4) & 0x0F))) == BAD)
        {
            *out_org = '\0';
            return WRONG_FORMAT;
        }
        if ((*out++ = ENCODE64(((in[1] << 2) & 0x3C) | ((in[2] >> 6) & 0x03))) == BAD)
        {
            *out_org = '\0';
            return WRONG_FORMAT;
        }
        if ((*out++ = ENCODE64(in[2] & 0x3F)) == BAD)
        {
            *out_org = '\0';
            return WRONG_FORMAT;
        }
    }

    // encode tail-fragment if exist
    if (inlen > 0)
    {
        char fragment;

        // update output length and check overflow
        outlen += 4;
        if (outlen >= maxlen && maxlen)
        {
            *out_org = '\0';
            return OUTPUT_OVERFLOW;
        }

        if ((*out++ = ENCODE64((in[0] >> 2) & 0x3F)) == BAD)
        {
            *out_org = '\0';
            return WRONG_FORMAT;
        }
        fragment = (in[0] << 4) & 0x30;
        if (inlen > 1)
        {
            fragment |= (in[1] >> 4) & 0x0F;
        }
        if ((*out++ = ENCODE64(fragment)) == BAD)
        {
            *out_org = '\0';
            return WRONG_FORMAT;
        }
        if (inlen < 2)
        {
            *out++ = '=';
        }
        else
        {
            if ((*out++ = ENCODE64((in[1] << 2) & 0x3C)) == BAD)
            {
                *out_org = '\0';
                return WRONG_FORMAT;
            }
        }
        *out++ = '=';
    }

    // terminate the output string
    *out = '\0';
    return outlen;
}
```

## 解密

```c
int base64_decode(unsigned char *out, unsigned char *in, int inlen, int maxlen)
{
    //~int inlen = strlen (in);
    int outlen = 0;
    int over = 0; // decode over flag
    register char digit0, digit1, digit2, digit3;
    unsigned char *out_org = out;
    memset(out, 0x00, maxlen);

    // check if arguments valid
    if (!out || !in)
    {
        return INVALID_ARG;
    }

    // decode each four base64 characters
    for (; inlen >= 4; inlen -= 4, in += 4)
    {
        // update output length and check overflow
        if (++outlen >= maxlen && maxlen)
        {
            *out_org = '\0';
            return OUTPUT_OVERFLOW;
        }

        if ((digit0 = DECODE64(in[0])) == BAD)
        {
            *out_org = '\0';
            return WRONG_FORMAT;
        }
        if ((digit1 = DECODE64(in[1])) == BAD)
        {
            *out_org = '\0';
            return WRONG_FORMAT;
        }
        *out++ = ((digit0 << 2) & 0xFC) | ((digit1 >> 4) & 0x03);

        if (in[2] != '=')
        {
            // update output length and check overflow
            if (++outlen >= maxlen && maxlen)
            {
                *out_org = '\0';
                return OUTPUT_OVERFLOW;
            }

            if ((digit2 = DECODE64(in[2])) == BAD)
            {
                *out_org = '\0';
                return WRONG_FORMAT;
            }
            *out++ = ((digit1 << 4) & 0xF0) | ((digit2 >> 2) & 0x0F);

            if (in[3] != '=')
            {
                // update output length and check overflow
                if (++outlen >= maxlen && maxlen)
                {
                    *out_org = '\0';
                    return OUTPUT_OVERFLOW;
                }

                if ((digit3 = DECODE64(in[3])) == BAD)
                {
                    *out_org = '\0';
                    return WRONG_FORMAT;
                }
                *out++ = ((digit2 << 6) & 0xC0) | (digit3 & 0x3F);
            }
            else
            {
                over = 1;
                break;
            }
        }
        else
        {
            over = 1;
            break;
        }
    }

    // there cannt have tail-fragment except after '='
    if (!over && inlen > 0)
    {
        *out_org = '\0';
        return WRONG_FORMAT;
    }

    // terminate the output string
    *out = '\0';

    return outlen;
}
```
