

## 1 -- 剑指 Offer 14- I. 剪绳子

>给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

``` rust
pub fn cutting_rope(n: i32) -> i32 {
  if n == 1 || n == 2 {
    return 1;
  }
  if n == 3 {
    return 2;
  }
  if n == 4 {
    return 4;
  }
  let three_pow = (n / 3) as u32;
  let l = n % 3;
  if l == 0 {
    return 3i32.pow(three_pow);
  } else if l == 1 {
    return 3i32.pow(three_pow - 1) * 4;
  } else {
    return 3i32.pow(three_pow) * l;
  }
}
/////////////////////////////////////////////////////////////
impl Solution {
    pub fn cutting_rope(n: i32) -> i32 {
        if n <= 3 {
            n - 1
        } else {
            i32::pow(3, n as u32 / 3) * 4 / (4 - n % 3)
        }
    }
}

```

## 2 -- 剑指 Offer 15- I. 剪绳子2

>给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。答案取模。

``` rust
pub fn cutting_rope(n: i32) -> i64 {
  if n == 2 {
    return 1;
  }
  if n == 3 {
    return 2;
  }
  let mut res = 1i64;
  let mut n:i64 = n as i64;
  while n > 4 {
    println!("{:?}",res);
    res =  res * 3 % 1000000007;
    n -=3;
  }
  return res * n % 1000000007
}
/////////////////////////////////////////////////////////////

```




领悟心得：
1.  规律题，也可以动态规划
2.  因为要取模，改用遍历，重点是 rust 需要注意整数溢出
