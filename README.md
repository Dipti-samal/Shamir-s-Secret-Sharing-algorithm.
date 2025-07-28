# Shamir-s-Secret-Sharing-algorithm.


import org.json.JSONObject;
import java.io.File;
import java.math.BigInteger;
import java.util.*;

public class ShamirSimple {
    public static void main(String[] args) throws Exception {
        System.out.println("Secret for Test Case 1: " + findSecret("testcase1.json"));
        System.out.println("Secret for Test Case 2: " + findSecret("testcase2.json"));
    }

    static BigInteger findSecret(String file) throws Exception {
        String content = new Scanner(new File(file)).useDelimiter("\\Z").next();
        JSONObject obj = new JSONObject(content);
        int k = obj.getJSONObject("keys").getInt("k");

        double[][] mat = new double[k][k + 1];
        int idx = 0;
        for (String key : obj.keySet()) {
            if (key.equals("keys") || idx >= k) continue;
            BigInteger x = new BigInteger(key);
            JSONObject point = obj.getJSONObject(key);
            int base = Integer.parseInt(point.getString("base"));
            BigInteger y = new BigInteger(point.getString("value"), base);
            for (int j = 0; j < k; j++) mat[idx][j] = x.pow(k - j - 1).doubleValue();
            mat[idx][k] = y.doubleValue();
            idx++;
        }

        return BigInteger.valueOf(Math.round(gauss(mat)[k - 1]));
    }

    static double[] gauss(double[][] a) {
        int n = a.length;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                double factor = a[j][i] / a[i][i];
                for (int k = i; k <= n; k++) a[j][k] -= factor * a[i][k];
            }
        }
        double[] x = new double[n];
        for (int i = n - 1; i >= 0; i--) {
            double sum = a[i][n];
            for (int j = i + 1; j < n; j++) sum -= a[i][j] * x[j];
            x[i] = sum / a[i][i];
        }
        return x;
    }
}
