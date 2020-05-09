# Base64

## 一 介绍

Base64 编码是将二进制转换为字符，用64个字符来标识任意的二进制数据，常用于 HTTP 加密，图片编码传输

可打印字符：在 ASCII 码中规定，0~33，128 这33个字符属于控制字符，32-127这95个字符属于可打印字符

转换方式：在 HTTP 协议下传输二进制数据需要将其转换成字符数据，而网络传输只能传输可打印字符，不能转换的就需要使用 Base64 进行转换

转换方法：

* 1 Byte（字节） = 8 bit（比特）
* Base64 定义了64（2^6）个可打印字符标识二进制的方法，也就是说 6 个 bit 的二进制数据可以用对应的字符替代表示
* 对于连续多个二进制数据，每 3 个字节一组进行转换，3 个字节，共 24 bit，然后再分为 4 个部分，每部分 6 bit，将 6 bit 二进制转换为 Base64 定义的字符即转换完成
* 如果二进制数据字节数不是3的倍数，Base64 将剩下的二进制数据补 0 至 3 的倍数，全 0 的用 = 替代

<https://blog.csdn.net/u013151053/article/details/81949810>

## 二 Java 实现

### 方式一 sun.misc

早期在 Java 上做 Base64 的编码与解码，使用 JDK sun.misc.BASE64Decoder 与 sun.misc.BASE64Encoder，示例如下

````java
import sun.misc.BASE64Encoder;
import sun.misc.BASE64Decoder;

public class Demo{
    public static void main(String[] args){
        encode();
        decode();
    }
    
    public void encode(){
        String plaintext = "You are my son !";
        BASE64Encoder encoder = new BASE64Encoder();
		String ciphertext = encoder.encode(plaintext.getBytes("UTF-8"));
		System.out.println("采用 sun.misc.BASE64Encoder 加密结果是：" + ciphertext);
    }
    
    public void decode(){
        String ciphertext = "WW91IGFyZSBteSBzb24gIQ==";
        BASE64Decoder decoder = new BASE64Decoder();
		byte[] plainbyte = decoder.decodeBuffer(ciphertext);
		String plaintext = new String(plainbyte,"UTF-8");
		System.out.println("采用 sun.misc.BASE64Encoder 解密结果是：" + plaintext);
    }
}
````

sun.misc 套件所提供的Base64功能，编码和解码的效率并不太好，而且在以后的Java版本可能不被支持，不建议使用。

### 方式二 Apache Commons Codec

Apache Commons Code 提供 Base64 的编码与解码功能，会使用到 org.apache.commons.codec.binary 下的 Base64

````java
import org.apache.commons.codec.binary.Base64;

public class Demo{
    public static void main(String[] args){
        encode();
        decode();
    }
    
    public void encode(){
        String plaintext = "You are my son !";
        Base64 ecoder = new Base64();
		String ciphertext = ecoder.encodeToString(plaintext.getBytes("UTF-8"));
		System.out.println("采用 org.apache.commons.codec.binary.Base64 加密结果是：\n" + ciphertext);
    }
    
    public void decode(){
        String ciphertext = "WW91IGFyZSBteSBzb24gIQ==";
        Base64 decoder = new Base64();
		byte[] plainbyte = decoder.decode(ciphertext);
		String plaintext = new String(plainbyte,"UTF-8");
		System.out.println("采用 org.apache.commons.codec.binary.Base64 解密结果是：\n" + plaintext);
    }
}
````

相比 sun.misc 更为精简，执行效率也比 sun.misc 高很多，但是需要额外引用 Apache Commons Codec 包

### 方式三 java.util.Base64

Java 8 的 java.util 包中提供了 Base64 操作类

````java
public class Demo{
    public static void main(String[] args){
        encode();
        decode();
    }
    
    public void encode(){
        String plaintext = "You are my son !";
        java.util.Base64.Encoder encoder = java.util.Base64.getEncoder();
		String ciphertext = encoder.encodeToString(plaintext.getBytes("UTF-8"));
		System.out.println("采用 java.util.Base64.Encoder 加密结果是：\n" + ciphertext);
    }
    
    public void decode(){
        String ciphertext = "WW91IGFyZSBteSBzb24gIQ==";
        java.util.Base64.Decoder deocder = java.util.Base64.getDecoder();
		byte[] plainbyte = deocder.decode(ciphertext);
		String plaintext = new String(plainbyte,"UTF-8");
		System.out.println("采用 java.util.Base64.Decoder 解密结果是：\n" + plaintext);
    }
}
````

与sun.misc套件和Apache Commons Codec所提供的Base64编解码器来比较的话，Java 8提供的Base64拥有更好的效能。实际测试编码与解码速度的话，Java 8提供的Base64，要比sun.mis c套件提供的还要快至少11倍，比Apache Commons Codec提供的还要快至少3倍。因此在Java上若要使用Base64，这个Java 8底下的java .util套件所提供的Base64类别绝对是首选！