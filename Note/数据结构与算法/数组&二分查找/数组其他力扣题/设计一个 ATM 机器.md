![[Pasted image 20250105114024.png]]
![[Pasted image 20250105114103.png]]

- atm可以直接用数组表示；
- 取钱的时候要判断实际的钞票是否有要取的那么多，要取两者最小值；
- `return new int[]{-1};`不要写错了

```java
class ATM {

    private int[] atm;
    private int[] cashNum = {20, 50, 100, 200, 500};

    public ATM() {
        atm = new int[5];;    
    }
    
    public void deposit(int[] banknotesCount) {
        for (int i = 0; i < 5; i++) {
            atm[i] += banknotesCount[i]; // 累加存入的钞票数量
        }
    }
    
    public int[] withdraw(int amount) {
        int[] needNum = {0, 0, 0, 0, 0};
        int need = 0;
        for(int i = 4; i >= 0; i--){
            if(atm[i] != 0){
                need = amount / cashNum[i];
                needNum[i] = Math.min(need, atm[i]); // 实际取出的张数取最小值
                amount -= needNum[i] * cashNum[i];
            }
        }
        if(amount != 0){
            return new int[]{-1};
        } else {
            for (int i = 0; i < 5; i++) {
                if (needNum[i] > 0) {
                    atm[i] -= needNum[i];
                }
            }
            return needNum;
        }
    }
}

/**
 * Your ATM object will be instantiated and called as such:
 * ATM obj = new ATM();
 * obj.deposit(banknotesCount);
 * int[] param_2 = obj.withdraw(amount);
 */
```
