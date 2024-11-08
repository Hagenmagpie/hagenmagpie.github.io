---
title: FOFA 通过ICO图片查询接口java\go实现
---

# FOFA 通过ICO图片查询接口java\go实现

FOFA 是白帽汇推出的一款网络空间搜索引擎,它支持通过ico图标进行搜索，网上很多基于python的接口调用示例，这里记录下java与go语言的掉员工示例，关键点在于计算图片编码期间 需要遵循MIME类型为Base64编码的标准 .

RFC 2045：用于MIME的Base64,详情见：https://datatracker.ietf.org/doc/html/rfc2045

## JAVA 版

```
package utils;

import cn.hutool.core.io.FileUtil;
import java.io.File;
import java.util.Base64;
import java.nio.charset.StandardCharsets;

public class FofaIconHash {

    // 主方法
    public static int fofaIconHash(byte[] src) {
        // 将字节数组转为 base64 编码
        String base64ed = insertNewLines(Base64.getEncoder().encodeToString(src), 76);
        // 计算 mmh3 hash（这里用 Hashing.murmur3_32() 来模拟）
        return mmh3Hash32(base64ed);
    }

    // 用于将 Base64 编码后的字符串按每 n 个字符插入换行符
    public static String insertNewLines(String s, int n) {
        StringBuilder result = new StringBuilder();
        for (int i = 0; i < s.length(); i += n) {
            int end = Math.min(i + n, s.length());
            result.append(s, i, end).append("\n");
        }
        return result.toString();
    }

    // 模拟 mmh3 Hash 的计算
    public static int mmh3Hash32(String input) {
        // 使用 Guava 库的 murmur3_32 来进行哈希计算
        return com.google.common.hash.Hashing.murmur3_32().hashString(input, StandardCharsets.UTF_8).asInt();
    }

    // 测试用例
    public static void main(String[] args) {
        String filePath="/opt/logo.ico";
        // 读取文件为字节数组
        byte[] fileBytes = FileUtil.readBytes(new File(filePath));
        int hashValue = fofaIconHash(fileBytes);
        System.out.println("Fofa Icon Hash: " + hashValue);
    }
}


```

## GO 版

```

func FofaIconHash(src []byte) int32 {
    // 遵循 MIME 类型 Base64 编码的标准 base64 encode 后做 mmh3 hash，与 fofa 保持一致
    base64ed := insertNewLines(base64.StdEncoding.EncodeToString(src), 76)
    return int32(mmh3.Hash32([]byte(base64ed)))
}

func insertNewLines(s string, n int) string {
    var result []string
    for i := 0; i < len(s); i += n {
        end := i + n
        if end > len(s) {
            end = len(s)
        }
        result = append(result, s[i:end])
    }
    return strings.Join(result, "\n") + "\n"
}

```
