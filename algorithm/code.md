领悟心得：
1. 善用数据结构，用 `HashMap` 来去重
2. 用 `match` 来处理 `Option`
3. 字符与字符串的各种 API，str 与 vec 的性能差异（？）
4. 操作原数组导致困难度增加，且更消耗内存。求中位数只需要得到合并数组的一半即可
5. 思路问题，优解思路：先消除偶数的情况（必然自相等），在遍历对比左右
6. 找规律而已

1：两数之和
=
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

===================

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


2：两数相加
=
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

===================

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



3：无重复字符的最长子串
=
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

===================

// 把 sub_str 改成 数组或者哈希

```



4：寻找两个正序数组的中位数
=
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


===================

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

5：最长回文子串
=
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

===================
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

6：Z 字形变换
=
>将一个给定字符串 s 根据给定的行数 numRows ，以从上往下、从左到右进行 Z 字形排列。
比如输入字符串为 "PAYPALISHIRING" 行数为 3 时，排列如下：
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

===================
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




> 来源：力扣（LeetCode）