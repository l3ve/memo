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