# 1
function inside function in can be used to solve the problem of not reusable local functions that is bashed by HMH and Other.

# 2

let i : Int? = nil
let result1: String? = i.map {_ in "hello"}
let result2: String? = i.flatMap {_ in "hello"}
let result3: String?? = i.map {_ in Optional("hello") }
let result4: String? = i.flatMap {_ in Optional("hello") }
