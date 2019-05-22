# In-class 6 group 1

##

## Diff

### Bug1

```bash
~/Documents/UW/csep590_sp19/debugging/bug1 [master] $ git diff
diff --git a/bug1/src/main/java/org/apache/commons/lang3/math/NumberUtils.java b/bug1/src/main/java/org/apache/commons/lang3/math/NumberUtils.java
index 4a1d845..d9b3c6d 100644
--- a/bug1/src/main/java/org/apache/commons/lang3/math/NumberUtils.java
+++ b/bug1/src/main/java/org/apache/commons/lang3/math/NumberUtils.java
@@ -1410,7 +1410,7 @@ public class NumberUtils {
             if (chars[i] == 'l'
                 || chars[i] == 'L') {
                 // not allowing L with an exponent or decimal point
-                return foundDigit && !hasExp;
+                return foundDigit && !hasExp && !hasDecPoint;
             }
             // last character is illegal
             return false;
diff --git a/bug1/src/test/java/org/apache/commons/lang3/math/TestClass4.java b/bug1/src/test/java/org/apache/commons/lang3/math/TestClass4.java
index 72da176..385925a 100644
--- a/bug1/src/test/java/org/apache/commons/lang3/math/TestClass4.java
+++ b/bug1/src/test/java/org/apache/commons/lang3/math/TestClass4.java
@@ -176,7 +176,11 @@ public class TestClass4 extends TestCase {
         assertTrue("isNumber(String)/createNumber(String) 24 Neg failed", !checkCreateNumber(val));
         val = "2.";
         assertTrue("isNumber(String) failed", NumberUtils.isNumber(val));
-        val = "1.1L";
+        
+    }
+
+    public void testMinimizeTest() {
+        String val = "1.1L";
         assertFalse("isNumber(String) failed", NumberUtils.isNumber(val));
     }
```
