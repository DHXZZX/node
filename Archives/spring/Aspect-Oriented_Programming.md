# Aspect-Oriented Programming with Spring

14982

15270



```java
public class SPT {
    public static void main(String[] args) {
        Random random = new Random(256);
        int[] list = new int[10000000];
        for (int i = 0; i < list.length; i++) {
            list[i] = random.nextInt();
        }
        List<Integer> result = new ArrayList<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < list.length; i++) {
            if (list[i] > 128) {
                result.add(list[i]);
            }
        }
        long end = System.currentTimeMillis();
        System.out.println(end - start);
    }
}

```

```java
public class TPS {
    public static void main(String[] args) {
        Random random = new Random(256);
        int[] list = new int[1000000];
        for (int i = 0; i < list.length; i++) {
            list[i] = random.nextInt();
        }
        List<Integer> result = new ArrayList<>();
	Arrays.sort(list);
        long start = System.currentTimeMillis();
        for (int i = 0; i < list.length; i++) {
            if (list[i] > 128) {
                result.add(list[i]);
            }
        }
        long end = System.currentTimeMillis();
        System.out.println(end - start);
    }
}

```

