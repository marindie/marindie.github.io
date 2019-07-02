---
toc: true
title: "Java: JAVA Encoding Decoding"
description: "JAVA JAVA Encoding Decoding"
categories: [Java]
tags: [Encoding]
redirect_from:
  - /2019/07/01/
---

> JAVA Encoding Decoding

### Encoding {#toc1}
```java

import java.io.UnsupportedEncodingException;
import java.nio.charset.Charset;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

public class EncodingTest {
	public byte[] convertToBinary(String input, String encoding) throws UnsupportedEncodingException {
	    byte[] encoded_input = Charset.forName(encoding)
	      .encode(input)
	      .array();
	    return encoded_input;
	}
	
	public String byteToString(byte[] b) {
		return IntStream.range(0, b.length)
	    .map(i -> b[i])
	    .mapToObj(e -> Integer.toBinaryString(e ^ 255))
	    .map(e -> String.format("%1$" + Byte.SIZE + "s", e).replace(" ", "0"))
	    .collect(Collectors.joining(" "));
	}
	

	public static void main(String[] args) throws UnsupportedEncodingException {
		EncodingTest run = new EncodingTest();
		byte[] a = run.convertToBinary("한글테스트","UTF-8");
		byte[] b = run.convertToBinary("한글테스트","x-windows-949");
		byte[] c = run.convertToBinary("한글테스트","EUC-KR");
				
		System.out.println(Charset.defaultCharset().displayName()); 
		System.out.println("========= Print Byte ==========="); 
		System.out.println("한글테스트 To UTF-8 인코딩"+run.convertToBinary("한글테스트","UTF-8")); 
		System.out.println("한글테스트 To x-windows-949 인코딩"+run.convertToBinary("한글테스트","x-windows-949")); 
		System.out.println("한글테스트 To EUC-KR 인코딩"+run.convertToBinary("한글테스트","EUC-KR"));
		
		System.out.println("========= Print Byte Length ==========="); 
		System.out.println("UTF-8 = "+run.convertToBinary("한글테스트","UTF-8").length); 
		System.out.println("x-windows-949 = "+run.convertToBinary("한글테스트","x-windows-949").length); 
		System.out.println("EUC-KR = "+run.convertToBinary("한글테스트","EUC-KR").length); 		
		
		System.out.println("========= Print Byte As String==========="); 
		System.out.println("UTF-8 = "+run.byteToString(run.convertToBinary("한글테스트","UTF-8"))); 
		System.out.println("x-windows-949 = "+run.byteToString(run.convertToBinary("한글테스트","x-windows-949"))); 
		System.out.println("EUC-KR = "+run.byteToString(run.convertToBinary("한글테스트","EUC-KR"))); 		
		
		System.out.println("========= UTF-8 로 변환한 Byte 내용을 Decoding 하여 확인 ==========="); 
		System.out.println("UTF-8 로 디코딩 값 = "+new String(a,"UTF-8"));
		System.out.println("UTF-8 로 디코딩 값 = "+new String(a,"EUC-KR"));
		
		System.out.println("========= EUC-KR 로 변환한 Byte 내용을 Decoding 하여 확인 ==========="); 
		System.out.println("UTF-8 로 디코딩 값 = "+new String(c,"UTF-8"));
		System.out.println("UTF-8 로 디코딩 값 = "+new String(c,"EUC-KR"));		
		
	}
}

```



[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io