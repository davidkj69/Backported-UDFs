Back-ported the following Hive UDFs from Hive 1.3 to Hive 0.14 (currently bundled with HDP 2.3.x)

===============================================

string sha1(string/binary)
sha(string/binary)
Calculates the SHA-1 digest for string or binary and returns the value as a hex string (as of Hive 1.3.0). Example: sha1('ABC') = '3c01bdbb26f358bab27f267924aa2c9a03fcfdb8'.

================================================

string	sha2(string/binary, int)	Calculates the SHA-2 family of hash functions (SHA-224, SHA-256, SHA-384, and SHA-512) (as of Hive 1.3.0). The first argument is the string or binary to be hashed. The second argument indicates the desired bit length of the result, which must have a value of 224, 256, 384, 512, or 0 (which is equivalent to 256). SHA-224 is supported starting from Java 8. If either argument is NULL or the hash length is not one of the permitted values, the return value is NULL. Example: sha2('ABC', 256) = 'b5d4045c3f466fa91fe2cc6abe79232a1a57cdf104f7a26e716e0a1e2789df78'.

================================================

binary	aes_encrypt(input string/binary, key string/binary)	Encrypt input using AES (as of Hive 1.3.0). Key lengths of 128, 192 or 256 bits can be used. 192 and 256 bits keys can be used if Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy Files are installed. If either argument is NULL or the key length is not one of the permitted values, the return value is NULL. Example: base64(aes_encrypt('ABC', '1234567890123456')) = 'y6Ss+zCYObpCbgfWfyNWTw=='.

================================================

binary	aes_decrypt(input binary, key string/binary)	Decrypt input using AES (as of Hive 1.3.0). Key lengths of 128, 192 or 256 bits can be used. 192 and 256 bits keys can be used if Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy Files are installed. If either argument is NULL or the key length is not one of the permitted values, the return value is NULL. Example: aes_decrypt(unbase64('y6Ss+zCYObpCbgfWfyNWTw=='), '1234567890123456') = 'ABC'.

================================================


Commands to use

[davidk@bd01vox110 ~]$ hdfs dfs -put Backported-UDFs-0.0.1.jar
 
hive> add jar Backported-UDFs-0.0.1.jar;
hive> CREATE function sha1 AS 'org.apache.hadoop.hive.ql.udf.UDFSha1' USING JAR 'hdfs:///user/davidk/Backported-UDFs-0.0.1.jar';
hive> select sha1('ABC');
 
hive> CREATE function aes_encrypt AS 'org.apache.hadoop.hive.ql.udf.generic.GenericUDFAesEncrypt' USING JAR 'hdfs:///user/davidk/Backported-UDFs-0.0.1.jar';
hive> select base64(aes_encrypt('ABC', '1234567890123456'));
 
hive> CREATE function aes_decrypt AS 'org.apache.hadoop.hive.ql.udf.generic.GenericUDFAesDecrypt' USING JAR 'hdfs:///user/davidk/Backported-UDFs-0.0.1.jar';
hive> select aes_decrypt(unbase64('y6Ss+zCYObpCbgfWfyNWTw=='), '1234567890123456');
 
hive> CREATE function sha2 AS 'org.apache.hadoop.hive.ql.udf.generic.GenericUDFSha2' USING JAR 'hdfs:///user/davidk/Backported-UDFs-0.0.1.jar';
hive> select sha2('ABC', 256);