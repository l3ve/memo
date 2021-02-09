领悟心得：
1. 善用数据结构，用 `HashMap` 来去重
2. 用 `match` 来处理 `Option`
3. 字符与字符串的各种 API，str 与 vec 的性能差异（？）

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




> 来源：力扣（LeetCode）