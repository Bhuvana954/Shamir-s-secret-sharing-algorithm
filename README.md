import java.math.BigInteger;
import java.util.*;
import java.util.stream.Collectors;

class Main {

    public static BigInteger findSecretConstant(Map<String, Object> testcase) {
        Map<String, Integer> keys = (Map<String, Integer>) testcase.get("keys");
        int n = keys.get("n");
        int k = keys.get("k");
        int degree = k - 1;

        List<BigInteger[]> points = new ArrayList<>();

  
        for (String key : testcase.keySet()) {
            if (!key.equals("keys")) {
                Map<String, String> pointData = (Map<String, String>) testcase.get(key);
                int x = Integer.parseInt(key);
                int base = Integer.parseInt(pointData.get("base"));
                BigInteger y = new BigInteger(pointData.get("value"), base);
                points.add(new BigInteger[] { BigInteger.valueOf(x), y });
            }
        }

        
        if (points.size() < k) {
            System.out.println("Error: Not enough points to reconstruct the secret.");
            return null;
        }

  
        List<BigInteger[]> selectedPoints = points.subList(0, k);

      
        BigInteger secret = lagrangeInterpolation(BigInteger.ZERO, selectedPoints);

        return secret;
    }

    public static BigInteger lagrangeInterpolation(BigInteger x, List<BigInteger[]> points) {
        BigInteger result = BigInteger.ZERO;

        for (int i = 0; i < points.size(); i++) {
            BigInteger xi = points.get(i)[0];
            BigInteger yi = points.get(i)[1];

            BigInteger term = yi;
            for (int j = 0; j < points.size(); j++) {
                if (i != j) {
                    BigInteger xj = points.get(j)[0];
                    term = term.multiply(x.subtract(xj)).divide(xi.subtract(xj));
                }
            }
            result = result.add(term);
        }

        return result;
    }

    public static void main(String[] args) {
        Map<String, Object> testcase1 = new HashMap<>();
        testcase1.put("keys", Map.of("n", 4, "k", 3));
        testcase1.put("1", Map.of("base", "10", "value", "4"));
        testcase1.put("2", Map.of("base", "2", "value", "111"));
        testcase1.put("3", Map.of("base", "10", "value", "12"));
        testcase1.put("6", Map.of("base", "4", "value", "213"));

        Map<String, Object> testcase2 = new HashMap<>();
        testcase2.put("keys", Map.of("n", 10, "k", 7));
        testcase2.put("1", Map.of("base", "6", "value", "13444211440455345511"));
        testcase2.put("2", Map.of("base", "15", "value", "aed7015a346d63"));
        testcase2.put("3", Map.of("base", "15", "value", "6aeeb69631c227c"));
        testcase2.put("4", Map.of("base", "16", "value", "e1b5e05623d881f"));
        testcase2.put("5", Map.of("base", "8", "value", "316034514573652620673"));
        testcase2.put("6", Map.of("base", "3", "value", "2122212201122002221120200210011020220200"));
        testcase2.put("7", Map.of("base", "3", "value", "20120221122211000100210021102001201112121"));
        testcase2.put("8", Map.of("base", "6", "value", "20220554335330240002224253"));
        testcase2.put("9", Map.of("base", "12", "value", "45153788322a1255483"));
        testcase2.put("10", Map.of("base", "7", "value", "1101613130313526312514143"));

        BigInteger secret1 = findSecretConstant(testcase1);
        if (secret1 != null) {
            System.out.println("Secret for testcase 1: " + secret1);
        }

        BigInteger secret2 = findSecretConstant(testcase2);
        if (secret2 != null) {
            System.out.println("Secret for testcase 2: " + secret2);
        }
    }
}
