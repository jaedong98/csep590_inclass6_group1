# In-class 6 group 1

## Questions

1. 
    1. Test output: We were able to infer the method name, but not the class, from the assertion log output.
    2. Vertical partition: We were able to infer the buggy class, but there were multiple methods flagged.
    3. Assertion: We were able to infer the buggy class and the buggy method.
    4. Method: We were able to narrow down the buggy statement once we actually investigated the method itself.
2. Better categorize the tests and make them more targeted to specific classes and methods. Since the failing test hit several different methods, it was hard to narrow down the offending method until we extracted the failing assertion into its own test. After that, the vertical partition was much cleaner and had a more targeted section of red. A downside of this would be longer test runs due to more tests being set up and potentially more engineering overhead.
3. The vertical partition had a narrowed down section of red after minimizing the failing test. This helped narrow down to the actual offending method. The csv also had fewer lines flagged as suspicious.
4. The vertical partition after minimizing the test helped us narrow down the failing method, then manual analysis of the method led us to the bug and ultimately the fix.
5. The second bug was easiest to fix, since the test framework gave us type information in the output, leading us to realize a mismatch between the method's return type and the test's expected return type. We found the third bug the hardest to localize, since the failing test was an exception instead of an assertion failure.
6. 
    1. Bug1: tied for first
    2. Bug2: not present, though the method's only line is the most suspicious
    3. Bug3: tied for second

## Diff

### Bug1

```bash
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
```

### Minimized test

```bash
diff --git a/bug1/src/test/java/org/apache/commons/lang3/math/TestClass4.java b/bug1/src/test/java/org/apache/commons/lang3/math/TestClass4.java
index 72da176..385925a 100644
--- a/bug1/src/test/java/org/apache/commons/lang3/math/TestClass4.java
+++ b/bug1/src/test/java/org/apache/commons/lang3/math/TestClass4.java
@@ -176,7 +176,11 @@ public class TestClass4 extends TestCase {
         assertTrue("isNumber(String)/createNumber(String) 24 Neg failed", !checkCreateNumber(val));
         val = "2.";
         assertTrue("isNumber(String) failed", NumberUtils.isNumber(val));
-        val = "1.1L";
+    }
+
+    public void testMinimizeTest() {
+        String val = "1.1L";
         assertFalse("isNumber(String) failed", NumberUtils.isNumber(val));
     }
```

### Bug2

```bash
diff --git a/bug2/src/main/java/org/apache/commons/lang3/SystemUtils.java b/bug2/src/main/java/org/apache/commons/lang3/SystemUtils.java
index 0c73f42..2116f7c 100644
--- a/bug2/src/main/java/org/apache/commons/lang3/SystemUtils.java
+++ b/bug2/src/main/java/org/apache/commons/lang3/SystemUtils.java
@@ -1669,7 +1669,7 @@ public class SystemUtils {
      *
      * @return the version, for example 131 for Java 1.3.1
      */
-    static float toJavaVersionInt(String version) {
+    static int toJavaVersionInt(String version) {
         return toVersionInt(toJavaVersionIntArray(version, JAVA_VERSION_TRIM_SIZE));
     }
```

### Bug3

```bash
diff --git a/bug3/src/java/org/apache/commons/lang/math/NumberUtils.java b/bug3/src/java/org/apache/commons/lang/math/NumberUtils.java
index c0f06a4..ec5a44e 100644
--- a/bug3/src/java/org/apache/commons/lang/math/NumberUtils.java
+++ b/bug3/src/java/org/apache/commons/lang/math/NumberUtils.java
@@ -451,8 +451,8 @@ public class NumberUtils {
                 case 'L' :
                     if (dec == null
                         && exp == null
-                        && isDigits(numeric.substring(1))
-                        && (numeric.charAt(0) == '-' || Character.isDigit(numeric.charAt(0)))) {
+                        && ((isDigits(numeric.substring(1))
+                        && numeric.charAt(0) == '-') || Character.isDigit(numeric.charAt(0)))) {
                         try {
                             return createLong(numeric);
                         } catch (NumberFormatException nfe) {
```
