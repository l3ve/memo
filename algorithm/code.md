## 1：两数之和

>给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 的那 两个 整数，并返回它们的数组下标。
>
>你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。
>
>你可以按任意顺序返回答案。


``` rust
impl Solution {
    pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
    let mut res: Vec<i32> = vec![0,0];

    for (i, a) in nums.iter().enumerate() {
        let _nums: Vec<i32> = nums.clone().drain(i+1..).collect();
        for (ii, b) in _nums.iter().enumerate() {
            if a + b == target {
                res[0] = i as i32;
                res[1] = (ii + i + 1) as i32;
                break;
            }
        }
    }
    return res;
    }
}

/////////////////////////////////////////////////////////////

use std::collections::HashMap;
impl Solution {
    pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
        let mut map : HashMap<i32,i32> = HashMap::with_capacity(nums.len());

        for (i, x) in nums.into_iter().enumerate() {
            if let Some(&j) = map.get(&(target-x)){
                return vec![j, i as i32];
            } else{
                map.insert(x, i as i32);
                continue;
            }
        }

        vec![]
    }
}
```


## 2：两数相加

>给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。
>
>请你将两个数相加，并以相同形式返回一个表示和的链表。
>
>你可以假设除了数字 0 之外，这两个数都不会以 0 开头。


``` rust
// Definition for singly-linked list.
// #[derive(PartialEq, Eq, Clone, Debug)]
// pub struct ListNode {
//   pub val: i32,
//   pub next: Option<Box<ListNode>>
// }
//
// impl ListNode {
//   #[inline]
//   fn new(val: i32) -> Self {
//     ListNode {
//       next: None,
//       val
//     }
//   }
// }
impl Solution {
  pub fn add_two_numbers(
    l1: Option<Box<ListNode>>,
    l2: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        let mut l1 = l1;
        let mut l2 = l2;
        let mut carry = 0;

        let _1 = l1.unwrap_or(Box::new(ListNode::new(0)));
        let _2 = l2.unwrap_or(Box::new(ListNode::new(0)));

        let mut _3 = _1.val + _2.val;
        if _3 >= 10 {
            carry = 1;
            _3 = _3 - 10;
        }
        let mut res = Some(Box::new(ListNode::new(_3)));
        l1 = _1.next;
        l2 = _2.next;

        let mut point = &mut res;
        while l1.is_some() || l2.is_some() || carry != 0 {
            let _1 = l1.unwrap_or(Box::new(ListNode::new(0)));
            let _2 = l2.unwrap_or(Box::new(ListNode::new(0)));

            let mut _3 = _1.val + _2.val + carry;
            carry = 0;
            if _3 >= 10 {
                carry = 1;
                _3 = _3 - 10;
            }

            let cur_res = Some(Box::new(ListNode::new(_3)));
            point.as_mut().unwrap().next = cur_res;
            point = &mut point.as_mut().unwrap().next;

            l1 = _1.next;
            l2 = _2.next;
        }
        res
    }
}

/////////////////////////////////////////////////////////////

impl Solution {
    pub fn add_two_numbers(l1: Option<Box<ListNode>>, l2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut result = None;
        let mut tail = &mut result;
        let mut t = (l1, l2, 0, 0); // (list1, list2, sum, carry)
        loop {
            t = match t {
                (None, None, _, 0) => break,
                (None, None, _, carry) => (None, None, carry, 0),
                (Some(list), None, _, carry) | (None, Some(list), _, carry) if list.val + carry >= 10 => {
                    (list.next, None, list.val + carry - 10, 1)
                }
                (Some(list), None, _, carry) | (None, Some(list), _, carry) => {
                    (list.next, None, list.val + carry, 0)
                }
                (Some(l1), Some(l2), _, carry) if l1.val + l2.val + carry >= 10 => {
                    (l1.next, l2.next, l1.val + l2.val + carry - 10, 1)
                }
                (Some(l1), Some(l2), _, carry) => {
                    (l1.next, l2.next, l1.val + l2.val + carry, 0)
                }
            };

            *tail = Some(Box::new(ListNode::new(t.2)));
            tail = &mut tail.as_mut().unwrap().next;
        }
        result
    }
}

```



## 3：无重复字符的最长子串

>给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。


``` rust
pub fn length_of_longest_substring(s: String) -> i32 {
  let mut count = 0;
  let len = s.len();
  let mut sub_str = String::new();

  for i in 0..len {
    let _s = &s[i..i + 1];
    if let Some(point) = sub_str.find(_s) {
      sub_str.drain(..point + 1);
      sub_str.push_str(&_s);
    } else {
      sub_str.push_str(&_s);
    }
    if count < sub_str.len() {
      count = sub_str.len();
    }
  }

  count as i32
}

/////////////////////////////////////////////////////////////

// 把 sub_str 改成 数组或者哈希

```



## 4：寻找两个正序数组的中位数

>给定两个大小分别为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。请你找出并返回这两个正序数组的 中位数。


``` rust
pub fn find_median_sorted_arrays(mut nums1: Vec<i32>, mut nums2: Vec<i32>) -> f64 {
  let mut _mix_arr: Vec<i32> = vec![];
  let is_even = (nums1.len() + nums2.len()) % 2 == 0;
  let mid_len = (nums1.len() + nums2.len()) / 2 + 1;
  if nums1.is_empty() || nums2.is_empty() {
    _mix_arr.append(&mut nums1);
    _mix_arr.append(&mut nums2);
  } else {
    let mut _a: Vec<i32> = nums1.drain(..1).collect();
    let mut _b: Vec<i32> = nums2.drain(..1).collect();
    loop {
      if _a[0] < _b[0] {
        _mix_arr.append(&mut _a);
        if _mix_arr.len() == mid_len || nums1.is_empty() {
          _mix_arr.append(&mut _b);
          _mix_arr.append(&mut nums2);
          break;
        }
        _a = nums1.drain(..1).collect();
      } else {
        _mix_arr.append(&mut _b);
        if _mix_arr.len() == mid_len || nums2.is_empty() {
          _mix_arr.append(&mut _a);
          _mix_arr.append(&mut nums1);
          break;
        }
        _b = nums2.drain(..1).collect();
      }
    }
  }
  if is_even {
    (_mix_arr[mid_len - 2] + _mix_arr[mid_len -1]) as f64 / 2_f64
  } else {
    return _mix_arr[mid_len - 1] as f64;
  }
}


/////////////////////////////////////////////////////////////

pub fn find_median_sorted_arrays(nums1: Vec<i32>, nums2: Vec<i32>) -> f64 {
  if nums1.len() + nums2.len() == 0 {
    return 0.0;
  }
  let is_even = (nums1.len() + nums2.len()) % 2 == 0;
  let len = (nums1.len() + nums2.len()) / 2 + 1;
  let mut nums = vec![0; len + 1];
  let mut i: usize = 0;
  let mut j: usize = 0;
  while &i + &j < len {
    if i >= nums1.len() {
      nums[i + j] = nums2[j];
      j = j + 1;
    } else if j >= nums2.len() {
      nums[i + j] = nums1[i];
      i = i + 1;
    } else if nums1[i] < nums2[j] {
      nums[i + j] = nums1[i];
      i = i + 1;
    } else if nums1[i] > nums2[j] {
      nums[i + j] = nums2[j];
      j = j + 1;
    } else {
      nums[i + j] = nums1[i];
      i = i + 1;
      nums[i + j] = nums2[j];
      j = j + 1;
    }
  }
  if is_even {
    (nums[len - 1] + nums[len - 2]) as f64 / 2.0
  } else {
    nums[len - 1] as f64
  }
}
```

## 5：最长回文子串

>给你一个字符串 s，找到 s 中最长的回文子串。

``` rust
pub fn longest_palindrome(s: String) -> String {
  let s: Vec<&str> = s.split("").collect();
  let mut s: String = s.join("#");
  let str_len = s.len();
  let mut mid_point = 1;
  let mut point = (0, 2);
  loop {
    if mid_point >= str_len - 1 - (point.1 - point.0) / 2 {
      break;
    }
    let mut t_point = (mid_point, mid_point);
    loop {
      t_point = (t_point.0 - 1, t_point.1 + 1);
      let l_str = s.get(t_point.0..t_point.0 + 1);
      let r_str = s.get(t_point.1..t_point.1 + 1);
      if l_str == r_str {
        if (t_point.1 - t_point.0) > (point.1 - point.0) {
          point = t_point;
        }
      } else {
        break;
      }
      if t_point.0 == 0 || t_point.1 == str_len - 1 {
        break;
      }
    }
    mid_point = mid_point + 1;
  }
  s = s.drain(point.0..point.1 + 1).collect();
  let s: Vec<&str> = s.split("#").collect();
  s.join("")
}

/////////////////////////////////////////////////////////////

impl Solution {
    pub fn longest_palindrome(s: String) -> String {
        let seq: Vec<char> = s.chars().collect();
        let len = seq.len();
        if len < 1 {
            return s;
        }
        let (mut idx, mut curr_len, mut curr_start, mut curr_end) = (0,0,0,0);
        while idx < len {
            let (mut i, mut j) = (idx, idx);
            let ch = seq[idx];
            // handle same char
            while i > 0 && seq[i - 1] == ch {
                i -= 1
            }
            while j < len - 1 && seq[j + 1] == ch {
                j += 1
            }
            idx = j + 1;
            while i > 0 && j < len - 1 && seq[i - 1] == seq[j + 1] {
                i -= 1;
                j += 1;
            }
            let max_len = j - i + 1;
            if max_len > curr_len {
                curr_len = max_len;
                curr_start = i;
                curr_end = j;
            }
            if max_len >= len - 1 {
                break;
            }
        }
        s[curr_start..curr_end + 1].to_owned()
    }
}

```

## 6：Z 字形变换

>将一个给定字符串 s 根据给定的行数 numRows ，以从上往下、从左到右进行 Z 字形排列。
>
>比如输入字符串为 "PAYPALISHIRING" 行数为 3 时，排列如下：
>```
>P   A   H   N
>A P L S I I G
>Y   I   R
>```
>之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如："PAHNAPLSIIGYIR"。

``` rust
pub fn convert(s: String, num_rows: i32) -> String {
  let mut res = String::new();
  let n = num_rows * 2 - 2;
  if n <= 1 {
    return s;
  }
  let loop_count = s.len() as i32 / n + 1;
  for _i in 0..num_rows {
    for _ii in 0..loop_count {
      let _index = (_i + n * _ii) as usize;
      if _index >= s.len() {
        break;
      }
      let _str = s.get(_index.._index + 1).unwrap();
      res.push_str(_str);
      if _i != num_rows - 1 && _i != 0 {
        let _iii = _index + (n - 2 * _i) as usize;
        if _iii >= s.len() {
          break;
        }
        let _str = s.get(_iii.._iii + 1).unwrap();
        res.push_str(_str);
      }
    }
  }
  res
}

/////////////////////////////////////////////////////////////

pub fn convert(s: String, num_rows: i32) -> String {
  if num_rows <= 1 {
    return s;
  }
  let n = s.len();
  let num_rows = num_rows as usize;
  let cycle_len = 2 * num_rows - 2;
  let s = s.into_bytes();
  let mut ret = String::new();
  for i in 0..num_rows {
    let mut j = 0;
    while j + i < n {
      ret.push(s[i + j] as char);
      if i != 0 && i != num_rows - 1 && j + cycle_len - i < n {
        ret.push(s[j + cycle_len - i] as char);
      }
      j += cycle_len;
    }
  }
  ret
}
```

## 7：整数反转

>给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。
>
>如果反转后整数超过 32 位的有符号整数的范围 [−231,  231 − 1] ，就返回 0。
>
>假设环境不允许存储 64 位整数（有符号或无符号）。

``` rust
pub fn reverse(x: i32) -> i32 {
  let min = x < 0;
  let option_abs = x.checked_abs();
  if option_abs.is_none() {
    return 0
  }
  let str = option_abs.unwrap().to_string();
  let mut n = 0;
  let mut res = 0_i32;
  for s in str.chars() {
    let option_pow = 10_i32.checked_pow(n);
    if option_pow.is_none() {
      return 0;
    }
    let option_mul = (s.to_digit(10).unwrap() as i32).checked_mul(option_pow.unwrap());
    if option_mul.is_none() {
      return 0;
    }
    let option_res = res.checked_add(option_mul.unwrap());
    if option_res.is_none() {
      return 0;
    }
    res = option_res.unwrap();
    n = n + 1;
  }
  if min {
    res = -res;
  }
  res
}

/////////////////////////////////////////////////////////////

pub fn reverse(x: i32) -> i32 {
        let mut num = x;
        let mut reverse_num: i32 = 0;
        while num != 0 {
            // 计算出每一位的数字
            let digit = num % 10;
            // 每次计算出一位数字后除以 10
            num /= 10;
            if let Some(rev_num) = reverse_num.checked_mul(10) {
                // 将反转后的数字增加 10 倍，把反转后的数字上累加到后面
                reverse_num = rev_num + digit;
            } else {
                // 越界，直接返回
                return 0;
            }
        }
        reverse_num
    }
```

## 8：字符串转换整数 (atoi)

>请你来实现一个 myAtoi(string s) 函数，使其能将字符串转换成一个 32 位有符号整数（类似 C/C++ 中的 atoi 函数）。
>
>函数 myAtoi(string s) 的算法如下：
>
>* 读入字符串并丢弃无用的前导空格
>* 检查下一个字符（假设还未到字符末尾）为正还是负号，读取该字符（如果有）。 确定最终结果是负数还是正数。 如果两者都不存在，则假定结果为正。
>* 读入下一个字符，直到到达下一个非数字字符或到达输入的结尾。字符串的其余部分将被忽略。
>* 将前面步骤读入的这些数字转换为整数（即，"123" -> 123， "0032" -> 32）。如果没有读入数字，则整数为 0 。必要时更改符号（从步骤 2 开始）。
>* 如果整数数超过 32 位有符号整数范围 [−231,  231 − 1] ，需要截断这个整数，使其保持在这个范围内。具体来说，小于 −231 的整数应该被固定为 −231 ，大于 231 − 1 的整数应该被固定为 231 − 1 。
>* 返回整数作为最终结果。
>
>注意：
>
>* 本题中的空白字符只包括空格字符 ' ' 。
>* 除前导空格或数字后的其余字符串外，请勿忽略 任何其他字符。

``` rust
pub fn my_atoi(s: String) -> i32 {
  let mut res = 0_i32;
  let mut _s = s.trim_start().to_string();
  let mut is_minus = false;
  if _s.starts_with(|c| c == '-' || c == '+') {
    let frist_c: String = _s.drain(..1).collect();
    if frist_c == "-" {
      is_minus = true;
    }
  };
  for c in _s.chars() {
    if c.is_ascii_digit() {
      if let Some(v) = res.checked_mul(10) {
        let int_c = c.to_digit(10).unwrap() as i32;
        let mut o_res = None;
        if is_minus {
          o_res = v.checked_sub(int_c);
          if o_res.is_none() {
            res = i32::MIN;
            break;
          }
        } else {
          o_res = v.checked_add(int_c);
          if o_res.is_none() {
            res = i32::MAX;
            break;
          }
        }
        res = o_res.unwrap();
      } else {
        if is_minus {
          res = i32::MIN;
          break;
        } else {
          res = i32::MAX;
          break;
        }
      }
    } else {
      break;
    }
  }
  res
}
/////////////////////////////////////////////////////////////

pub fn my_atoi(s: String) -> i32 {
  let mut c = s.trim_start().chars();
  let mut r = 0;
  let mut sign = 1;
  if let Some(first) = c.next() {
    match first {
      '-' => sign = -1,
      '+' => {}
      '0'..='9' => {
        r = first as i32 - '0' as i32;
      }
      _ => return r,
    }
  } else {
    return r;
  };
  r *= sign;
  for x in c {
    match x {
      '0'..='9' => {
        let pop = (x as i32 - '0' as i32) * sign;
        if sign < 0 {
          if r < i32::MIN / 10 || (r == i32::MIN / 10 && pop < -8) {
            return i32::MIN;
          }
        } else if r > i32::MAX / 10 || (r == i32::MAX / 10 && pop > 7) {
          return i32::MAX;
        }
        r = r * 10 + pop;
      }
      _ => {
        break;
      }
    }
  }
  r
}

```

## 9：回文数

>给你一个整数 x ，如果 x 是一个回文整数，返回 true ；否则，返回 false 。
>
>回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。例如，121 是回文，而 123 不是。
>```
>输入：x = -121
>输出：false
>解释：从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
>```

``` rust

pub fn is_palindrome(x: i32) -> bool {
  if x.is_negative() {
    return false;
  }
  let mut s = x.to_string();
  let mut res = false;
  loop {
    if s.len() == 1 {
      res = true;
      break;
    }
    if s.pop().unwrap() != s.remove(0) {
      break;
    }
    if s.is_empty() {
      res = true;
      break;
    }
  }
  res
}


/////////////////////////////////////////////////////////////

pub fn is_palindrome(mut x: i32) -> bool {
  if x < 0 || (x % 10 == 0 && x != 0) {
    return false;
  }
  let mut r = 0;
  while x > r {
    r = r * 10 + x % 10;
    x /= 10;
  }
  x == r || x == r / 10
}

```

## 10：正则表达式匹配

>给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。
>
>* '.' 匹配任意单个字符
>* '*' 匹配零个或多个前面的那一个元素
>
>所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。

``` rust

pub fn is_match(s: String, p: String) -> bool {
  let s_l = s.len();
  let p_l = p.len();
  let g: Vec<bool> = vec![false; p_l + 1];
  let mut f: Vec<Vec<bool>> = vec![g; s_l + 1];
  f[0][0] = true;

  for i in 0..s_l + 1 {
    for j in 1..p_l + 1 {
      if &p[j - 1..j] == "*" {
        if match_str(i, j - 1, &s, &p) {
          f[i][j] = f[i - 1][j] || f[i][j - 2];
        } else {
          f[i][j] = f[i][j - 2];
        }
      } else if match_str(i, j, &s, &p) {
        f[i][j] = f[i - 1][j - 1];
      }
    }
  }
  f[s_l][p_l]
}

fn match_str(i: usize, j: usize, s: &str, p: &str) -> bool {
  if i == 0 {
    return false;
  }
  if &p[j - 1..j] == "." {
    return true;
  }
  return &s[i - 1..i] == &p[j - 1..j];
}


/////////////////////////////////////////////////////////////
use crate::OpTimes::{One, ZeroOrMore};
use crate::OpType::{AnyChar, OneChar};

#[derive(PartialEq)]
enum OpType {
  AnyChar,
  OneChar(char),
}

#[derive(PartialEq)]
enum OpTimes {
  One,
  ZeroOrMore,
}

impl Solution {
  pub fn is_match(s: String, p: String) -> bool {
    let mut pattern = vec![];
    for c in p.chars() {
      match c {
        '.' => pattern.push((AnyChar, One)),
        '*' => pattern.last_mut().unwrap().1 = ZeroOrMore,
        c => pattern.push((OneChar(c), One)),
      }
    }
    fn matcher(sub: &str, pat: &[(OpType, OpTimes)]) -> bool {
      let pp_may = pat.first();
      if pp_may.is_none() {
        return sub.len() == 0;
      }

      match pp_may.unwrap() {
        (ct, One) => {
          let mut chars = sub.chars();
          if let Some(t) = chars.next() {
            if *ct == AnyChar || OneChar(t) == *ct {
              return matcher(chars.as_str(), &pat[1..]);
            }
          }
          return false;
        }
        (ct, ZeroOrMore) => {
          if matcher(sub, &pat[1..]) {
            return true;
          }
          let mut chars = sub.chars();
          let mut c = chars.next();
          if c.is_none() {
            return false;
          }
          match ct {
            OneChar(exp) => {
              while c == Some(*exp) {
                if matcher(chars.as_str(), &pat[1..]) {
                  return true;
                }
                c = chars.next()
              }
            }
            AnyChar => {
              while c.is_some() {
                if matcher(chars.as_str(), &pat[1..]) {
                  return true;
                }
                c = chars.next()
              }
            }
          }
          return false;
        }
      }
    }
    matcher(&s, &pattern)
  }
}

```

## 11：盛最多水的容器

>给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0) 。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

``` rust
pub fn max_area(height: Vec<i32>) -> i32 {
  let mut left = 0;
  let mut l_max_height = height[left];
  let mut right = height.len() - 1;
  let mut r_max_height = height[right];
  let mut max_area = l_max_height.min(r_max_height) * (right - left) as i32;
  loop {
    if height[left] < height[right] {
      loop {
        left = left + 1;
        if left >= right {
          break;
        }
        let l_height = height[left];
        if l_height > l_max_height {
          l_max_height = l_height;
          break;
        }
      }
    } else {
      loop {
        right = right - 1;
        if left >= right {
          break;
        }
        let r_right = height[right];
        if r_right > r_max_height {
          r_max_height = r_right;
          break;
        }
      }
    }
    if left >= right {
      break;
    }
    let area = l_max_height.min(r_max_height) * (right - left) as i32;
    if area > max_area {
      max_area = area
    }
  }
  return max_area;
}

/////////////////////////////////////////////////////////////

```



## 12 -- 剑指 Offer 3: 数组中重复的数字

>找出数组中重复的数字。
>
>在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。


``` rust
use std::collections::HashSet;

pub fn find_repeat_number(nums: Vec<i32>) -> i32 {
  let mut _a: HashSet<&i32> = HashSet::new();
  for num in nums.iter() {
    if _a.contains(num) {
      return  *num;
    };
    _a.insert(num);
  }
  return 0;
}


/////////////////////////////////////////////////////////////

impl Solution {
    pub fn find_repeat_number(nums: Vec<i32>) -> i32 {
        let mut tmp = nums;
        tmp.sort();
        for (idx,val) in tmp.iter().enumerate(){
            if val==&tmp[idx+1]{
                return tmp[idx];
            }
        }
        return 0;
    }
}

```

## 13 -- 剑指 Offer 04. 二维数组中的查找

>在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

``` rust
pub fn find_number_in2_d_array(matrix: Vec<Vec<i32>>, target: i32) -> bool {
  let mut m = matrix;
  for _v in m.iter_mut() {
    _v.reverse();
    for v in _v.iter() {
      if target > *v {
        break
      }
      if target == *v {
        return true
      }
    }
  }
  return false;
}


/////////////////////////////////////////////////////////////

```


## 14 -- 剑指 Offer 05. 替换空格

>请实现一个函数，把字符串 s 中的每个空格替换成"%20"。

``` rust
pub fn replace_space(s: String) -> String {
  let mut _s = String::new();
  for c in s.chars() {
    if c == ' ' {
      _s.push_str("%20");
    } else {
      _s.push(c);
    }
  }
  return _s;
}

/////////////////////////////////////////////////////////////

```




## 15 -- 剑指 Offer 06. 从尾到头打印链表

>输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

``` rust
pub fn reverse_print(head: Option<Box<ListNode>>) -> Vec<i32> {
  let mut res = vec![];
  let mut v: Option<Box<ListNode>> = head;
  while v.is_some() {
    res.push(v.as_ref().unwrap().val);
    if v.as_ref().unwrap().next == None {
      break;
    }
    v = v.unwrap().next;
  }
  res.reverse();
  return res;
}

/////////////////////////////////////////////////////////////

```

## 16 -- 剑指 Offer 07. 重建二叉树（105. 从前序与中序遍历序列构造二叉树）

>输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

``` rust

pub fn build_tree(preorder: Vec<i32>, inorder: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
  return build(&preorder[..],&inorder[..])
}

pub fn build(preorder: &[i32], inorder: &[i32]) -> Option<Rc<RefCell<TreeNode>>> {

  if preorder.len() == 0 {
    return None;
  }
  let root = preorder[0];
  let mut tree = TreeNode::new(root);

  if preorder.len() == 1 {
    return Some(Rc::new(RefCell::new(tree)));
  }

  let root_index = inorder.iter().position(|&x| x == root).unwrap_or(0);

  let right_inorder = &inorder[root_index + 1..];
  let left_inorder = &inorder[0..root_index];
  let left_preorder = &preorder[1..left_inorder.len() + 1];
  let right_preorder = &preorder[left_preorder.len() + 1..];

  let left_tree = build(left_preorder, left_inorder);

  let right_tree = build(right_preorder, right_inorder);

  tree.left = left_tree;
  tree.right = right_tree;

  return Some(Rc::new(RefCell::new(tree)));
}


/////////////////////////////////////////////////////////////

```


## 17 -- 剑指 Offer 10- I. 斐波那契数列

>写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项（即 F(N)）。

``` rust
pub fn fib(mut n: i32) -> i32 {
  if n <= 1 {
    return n;
  }
  let mut pre: u128 = 0;
  let mut cur: u128 = 1;
  while n - 1 > 0 {
    cur = pre + cur;
    pre = cur - pre;
    n -= 1;
  }
  return (cur % 1000000007) as i32;
}

/////////////////////////////////////////////////////////////

```




## 18 -- 剑指 Offer 11. 旋转数组的最小数字

>把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。  

``` rust
pub fn min_array(numbers: Vec<i32>) -> i32 {
  let mut numbers = numbers;
  let len = numbers.len();
  if len == 1 {
    return numbers[0]
  }
  if len == 2 {
    return numbers[0].min(numbers[1]);
  }
  let mut s = 0;
  let mut e = len -1;
  loop {
    if s == e {
      return numbers[0]
    }
    if numbers[s] == numbers[s+1] {
      s += 1;
      continue;
    }
    if numbers[e] == numbers[e-1] {
      e -= 1;
      continue;
    }
    numbers = numbers.drain(s..e+1).collect();
    break
  }
  let len = numbers.len();
  let mut point = len / 2;

  loop {
    if numbers[point] <= numbers[len - 1] {
      if point == 0 {
        return numbers[0]
      }
      if numbers[point] < numbers[point - 1] {
        return numbers[point];
      }
      point -= 1;
    } else {
      if point == len -1 {
        return  numbers[len -1]
      }
      if numbers[point] > numbers[point + 1] {
        return numbers[point + 1];
      }
      point += 1;
    }
  }
}


/////////////////////////////////////////////////////////////

```





## 19 -- 剑指 Offer 12. 矩阵中的路径

>给定一个 m x n 二维字符网格 board 和一个字符串单词 word 。如果 word 存在于网格中，返回 true ；否则，返回 false 。
>
>单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。  

``` rust

use std::collections::HashSet;

#[derive(Debug)]
struct Path {
  position: (usize, usize, usize),
  direction: Vec<(usize, usize, usize)>,
}
impl Path {
  fn new(
    position: (usize, usize),
    index: usize,
    height: usize,
    width: usize,
    map: &Vec<Vec<char>>,
    word: &str,
  ) -> Path {
    let mut p = Path {
      direction: vec![],
      position: (position.0, position.1, index),
    };
    let index = index + 1;
    let next_char = word.chars().skip(index).next().unwrap();
    // top
    if position.0 > 0 && map[position.0 - 1][position.1] == next_char {
      p.direction.push((position.0 - 1, position.1, index));
    }
    // bottom
    if position.0 < height && map[position.0 + 1][position.1] == next_char {
      p.direction.push((position.0 + 1, position.1, index));
    }
    // left
    if position.1 > 0 && map[position.0][position.1 - 1] == next_char {
      p.direction.push((position.0, position.1 - 1, index));
    }
    // right
    if position.1 < width && map[position.0][position.1 + 1] == next_char {
      p.direction.push((position.0, position.1 + 1, index));
    }
    p
  }
}
pub fn exist(board: Vec<Vec<char>>, word: String) -> bool {
  let max_height = board.len() - 1;
  let max_width = board[0].len() - 1;
  let len = word.len();
  let mut stack = vec![];
  let mut enter = vec![];
  let mut hmap: HashSet<(usize, usize)> = HashSet::new();
  let first_char = &word.chars().next().unwrap();
  for (i, line) in board.iter().enumerate() {
    for (ii, c) in line.iter().enumerate() {
      if c == first_char {
        if len == 1 {
          return true;
        }
        let p = Path::new((i, ii), 0, max_height, max_width, &board, &word);
        if p.direction.len() > 0 {
          enter.push(p);
        }
      }
    }
  }

  for x in enter.into_iter() {
    hmap.clear();
    hmap.insert((x.position.0, x.position.1));
    stack.insert(0, x);
    loop {
      if stack.len() == 0 {
        break;
      }
      let mut cur_position = stack.remove(0);

      if cur_position.direction.len() == 0 {
        hmap.remove(&(cur_position.position.0, cur_position.position.1));
      } else {
        let next_position = cur_position.direction.remove(0);
        stack.insert(0, cur_position);
        if hmap.contains(&(next_position.0, next_position.1)) {
          continue;
        }
        if next_position.2 == len - 1 {
          return true;
        }
        let p = Path::new(
          (next_position.0, next_position.1),
          next_position.2,
          max_height,
          max_width,
          &board,
          &word,
        );
        if p.direction.len() > 0 {
          hmap.insert((p.position.0, p.position.1));
          stack.insert(0, p);
        }
      }
    }
  }
  return false;
}


/////////////////////////////////////////////////////////////

// 深度优先，标记数组防止重复访问
impl Solution {
    pub fn exist(board: Vec<Vec<char>>, word: String) -> bool {
        let chars = word.chars().collect::<Vec<char>>();
        let mut visit = vec![vec![false; board[0].len()]; board.len()];
        for i in 0..board.len() {
            for j in 0..board[0].len() {
                if find(&board, i, j, 0, &chars, &mut visit) {
                    return true;
                }
            }
        }
        false
    }
}

pub fn find(board: &Vec<Vec<char>>, i: usize, j: usize, idx: usize, chars: &Vec<char>, visit: &mut Vec<Vec<bool>>) -> bool {
    if i >= board.len() || j >= board[0].len() || visit[i][j] || board[i][j] != chars[idx] {
        return false;
    }
    if idx == chars.len() - 1 {
        return true;
    }
    visit[i][j] = true;
    let find = find(board, i + 1, j, idx + 1, chars, visit) ||
        if i > 0 { find(board, i - 1, j, idx + 1, chars, visit) } else { false } ||
        find(board, i, j + 1, idx + 1, chars, visit) ||
        if j > 0 { find(board, i, j - 1, idx + 1, chars, visit) } else { false };
    visit[i][j] = false;

    return find;
}

```




## 20 -- 剑指 Offer 13. 机器人的运动范围

>地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

``` rust

use std::collections::HashSet;

pub fn moving_count(m: i32, n: i32, k: i32) -> i32 {
  let mut stack = vec![];
  let mut res = 0;
  let mut set: HashSet<(i32, i32)> = HashSet::new();
  if k < 0 {
    return 1;
  }
  stack.push((0, 0));
  set.insert((0, 0));
  loop {
    if stack.is_empty() {
      break;
    }
    let p = stack.remove(0);
    let sum = get_sum((p.0, p.1));

    if sum <= k {
      res += 1;
      // left
      if p.1 > 0 {
        let left = (p.0, p.1 - 1);
        if !set.contains(&left) {
          set.insert(left);
          stack.push(left)
        }
      }
      // right
      if p.1 < n - 1 {
        let right = (p.0, p.1 + 1);
        if !set.contains(&right) {
          set.insert(right);
          stack.push(right)
        }
      }
      // bottom
      if p.0 < m - 1 {
        let bottom = (p.0 + 1, p.1);
        if !set.contains(&bottom) {
          set.insert(bottom);
          stack.push(bottom)
        }
      }
      // top
      if p.0 > 0 {
        let top = (p.0 - 1, p.1);
        if !set.contains(&top) {
          set.insert(top);
          stack.push(top)
        }
      }
    }
  }
  return res;
}

fn get_sum(num: (i32, i32)) -> i32 {
  let mut res0 = 0;
  let mut num = num;
  while num.0 >= 10 {
    res0 += num.0 % 10;
    num.0 /= 10;
  }
  res0 = res0 + num.0;
  let mut res1 = 0;
  while num.1 >= 10 {
    res1 += num.1 % 10;
    num.1 /= 10;
  }
  res1 = res1 + num.1;
  return res0 + res1;
}


/////////////////////////////////////////////////////////////
use std::cmp::max;

impl Solution {
    pub fn moving_count(m: i32, n: i32, k: i32) -> i32 {
        fn digitos_summare(n: usize) -> i32 {
            let mut n = n as i32;
            let mut res = 0i32;
            while n > 0 {
                res += n%10;
                n /= 10;
            }
            res
        }
        let mut rec = vec![vec![false; n as usize]; m as usize];
        let mut res = 0;
        rec[0][0] = true;
        for i in 0..rec.len() {
            for j in 0..rec[0].len() {
                if digitos_summare(i)+digitos_summare(j)<=k {
                    rec[i][j] = rec[max(1, i)-1][j] || rec[i][max(1, j)-1];
                    if rec[i][j] {
                        res += 1;
                    }
                }
            }
        }
        res
    }

}

```




领悟心得：
1. (两数之和) 善用数据结构，用 `HashMap` 来去重
2. (两数相加)用 `match` 来处理 `Option`
3. (无重复字符的最长子串)字符与字符串的各种 API，str 与 vec 的性能差异（？）
4. (寻找两个正序数组的中位数)操作原数组导致困难度增加，且更消耗内存。求中位数只需要得到合并数组的一半即可
5. (最长回文子串)思路问题，优解思路：先消除偶数的情况（必然自相等），在遍历对比左右
6. (Z 字形变换)找规律而已
7. (整数反转)match let 模式，边界的判断
8. (字符串转换整数 (atoi))边界判断是难点
9. (回文数)多用基础类型，效率和内存都会优化很多
10. (正则表达式匹配)动态规划还是不熟悉，优解也有点意思
11. (盛最多水的容器)从边界判断下手，本质是求集合
12. (数组中重复的数字)sort效率很高？
13. (二维数组中的查找)遍历的效率？while > for?(for有迭代器，如果类型复杂消耗会高？！)
14. (替换空格)rust 的字符类型是一种比较特殊的数据类型，需要熟悉（单引号是 char，双引号是字符切片）
15. (从尾到头打印链表)vec 的 push + reverse 比 insert 快
16. (重建二叉树)算法问题，记得多用引用少用所有权，且勿改变数据的类型，有利于运行的速度
17. (斐波那契数列)少用递归，多月队列或者循环代替
18. (旋转数组的最小数字)用二分法查询，且利用是有序数组可以更加高效，唯一要注意的是数组有多处相等的情况，如[2,2,2,1,2]与[2,1,2,2,2],此方法无法得知左遍历还是右遍历，故先消除相同的值。
19. 不想用递归，所以写复制了，很多地方可以优化，最优解好像没办法尾递归？！
20. 去重部分花太多内存了，优解都是创建二位数组，消耗上 set > vec ?


> 来源：力扣（LeetCode）