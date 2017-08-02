# 1. 命名
## 1.1 使用PascalCase为类型命名
## 1.2 使用PascalCase为枚举值命名
## 1.3 使用camelCase为函数命名
## 1.4 使用camelCase为属性或本地变量命名
## 1.5 不要为私有属性名添加_前缀
## 1.6 尽可能使用完整的单词拼写命名
## 1.7 不要出现需要猜测的命名

# 2. 类型
## 2.1 不要导出类型/函数，除非你要在不同的组件中共享它
## 2.2 不要在全局命名空间内定义类型/值
## 2.3 在一个文件里，类型定义应该出现在顶部
## 2.4 null和undefined： 使用 undefined，不要使用 null
## 2.5 不要使用any类型, 如果一定要使用, 请说明原因

# 3. 风格
## 3.1 分号可要可不要，但是风格要统一
## 3.2 相关功能的代码分模块，通过class导出
```javascript
// bad
function floorInternal() { }
export function floor() {
    return floorInternal()
}

// good
export class MyMath {
    // 内部可见
    private static floorInternal() { }

    // 外部可见
    public static floor() {
        return MyMath.floorInternal()
    }
}
```

## 3.3 禁止使用var, 首先考虑使用const, 其次是let
```javascript
//bad
var a = 1, b = 2 , c = 3;

// good
const [a, b, c] = [1, 2, 3];
```

## 3.4 使用arrow函数代替匿名函数表达式
## 3.5 只有需要的时候才把arrow函数的参数括起来
```
// bad
(x) => x + x

// good 
x => x + x
(x,y) => x + y
<T>(x: T, y: T) => x === y

```

## 3.6 小括号里开始不要有空白

## 3.7 逗号, 冒号, 分号后要有一个空格

```javascript
for (let i = 0, n = str.length; i < 10; i++) { 
    
}

if (x < 10) { 
    
}

function f(x: number, y: string): void { 
    
}
```

## 3.8 每个变量声明语句只声明一个变量

```javascript
// good 
let x = 1
let y = 2 

// bad
let x = 1, y = 2
```

### 3.9 使用async/await, 禁止使用回调函数。事件驱动的逻辑除外
```javascript 
// bad 
function readConfig(cb: Function) {
    fs.readFile("xxx.json", (err, s) => {
        if (err) {
            return cb()
        }
        let obj: any = JSON.parse(s)    // may throw exception
        return cb(obj)
    })
}

// good 
function readFineAsync(path: string): Promise<string> {
    return new Promise((resolve, reject) => {
        fs.readFile(path, (err, s) => {
            if (err)
                return reject(err)
            return resolve(s.toString())
        })
    })
}

async function readConfig() {
    try {
        let s = await readFineAsync("xxx.json")
        let obj: any = JSON.parse(s)    // may throw exception
        return obj
    } catch (e) {
        console.log(e)
    }
}
```
## 3.10 外部输入参数必须严格校验
外部参数主要指cgi提交的参数，外部获取的文件，外部网络通讯

```javascript
export class CrmUserOnCrm extends BaseHandler {
    public static async login(args: any): Promise<any> {
        const { username as phoneNo, password } = args
        if (!validator.isMobilePhone(phoneNo, "zh-CN")) {
            return { err: "invalid username" }
        }

        if (!(validator.isLength(password, { min: 4, max: 64 }) && validator.isAlphanumeric(password))) {
            return { err: "invalid password" }
        }

        ...

        return { msg: "ok" }
    }
}

router.handle("post", "/login", async (ctx, next) =>
    BaseHandler.handlerResult(ctx, await CrmUserOnCrm.login((ctx.request as any).body)))
```
## 3.11 内部参数可以简单校验
内部参数可以是，系统内部（包括跨进程），同一进程不同模块，本地文件，本地网络通讯等

```javascript
class LedInfo {
    ... 
    public setLedInfo(args: any) {
        const { carno, color, dir } = args
        assert(carno && color && dir)       // 内部使用函数：简单校验
        
        ... 
    }
}
```

## 3.12 对象的属性和方法尽量采用简洁表达法，这样易于描述和书写
```javascript
const ref = 'some value';

// bad 
const atom = {
    ref: ref,
    value: 1
}

//  good 
const atom = {
    ref,
    value: 1
}
```

## 3.13 相同的代码，出现超过两次，要提取成函数
## 3.14 异常及时处理
## 3.15 静态字符串一律使用单引号或反引号，不建议使用双引号。动态字符使用反引号
```javascript
//bad 
 const a = "foobar"
 const b = 'foo'+a+'bb'

// good 
const a = 'foobar'
const b = `foo${a}bar`
```

## 3.16 优先使用解构赋值
```javascript
const arr = [1, 2, 3, 4]

// bad
const first = arr[0]
const second = arr[1]

// good
const [first, second] = arr
```

## 3.17 函数的参数如果是对象的成员，优先使用解构赋值
```javascript
// bad
function getFullName(user) {
    const firstName = user.firstName
    const lastName = user.lastName
}

// good
function getFullName(obj) {
    const { firstName, lastName } = obj
}

// best
function getFullName({ firstName, lastName }) {}
```

## 3.18 如果函数返回多个值，优先使用对象的解构赋值，而不是数组的解构赋值
```javascript
function processInput(input) {
    return [left, right, top, bottom]
}

// good
function processInput(input) {
    return { left, right, top, bottom }
}

const { left, right } = processInput(input)
```

## 3.19 总是使用 class, 避免直接操作prototype 
```javascript
// bad
function Queue(contents = []) {
    this._queue = [...contents]
}
Queue.prototype.pop = function () {
    const value = this._queue[0]
    this._queue.splice(0, 1)
    return value
}

// good
class Queue {
    constructor(contents = []) {
        this._queue = [...contents]
    }
    pop() {
        const value = this._queue[0]
        this._queue.splice(0, 1)
        return value
    }
}
```

## 3.20 使用 extends 继承
```javascript

// bad
const inherits = require('inherits');
function PeekableQueue(contents) {
    Queue.apply(this, contents)
}
inherits(PeekableQueue, Queue)
PeekableQueue.prototype.peek = function () {
    return this._queue[0]
}

// good
class PeekableQueue extends Queue {
    peek() {
        return this._queue[0]
    }
}
```

## 3.21 方法可以返回 this 来帮助链式调用
```javascript
// bad
Jedi.prototype.jump = function () {
    this.jumping = true
    return true
}

Jedi.prototype.setHeight = function (height) {
    this.height = height
}

const luke = new Jedi()
luke.jump() // => true
luke.setHeight(20) // => undefined

// good
class Jedi {
    jump() {
        this.jumping = true
        return this
    }

    setHeight(height) {
        this.height = height
        return this
    }
}

const luke = new Jedi()
luke.jump().setHeight(20)
```

## 3.22 可以写一个自定义的 toString()方法，但要确保它能正常运行并且不会引起副作用
```javascript
class Jedi {
    constructor(options = {}) {
        this.name = options.name || 'no name'
    }

    getName() {
        return this.name
    }

    toString() {
        return `Jedi - ${this.getName()}`
    }
}
```

## 3.23 总是使用（import/export）而不是其他非标准模块系统
```javascript
// bad
const AirbnbStyleGuide = require('./AirbnbStyleGuide')
module.exports = AirbnbStyleGuide.es6

// ok
import AirbnbStyleGuide from './AirbnbStyleGuide'
export default AirbnbStyleGuide.es6

// best
import { es6 } from './AirbnbStyleGuide'
export default es6
```

## 3.24 不要使用通配符 import
```javascript 
// bad
import * as AirbnbStyleGuide from './AirbnbStyleGuide'

// good
import AirbnbStyleGuide from './AirbnbStyleGuide'
```

## 3.25 不要从 import 中直接 export
```javascript
// bad
export { es6 as default } from './airbnbStyleGuide'

// good
import { es6 } from './AirbnbStyleGuide'
export default es6
```

## 3.26 使用 . 来访问对象的属性
```javascript
const student = {
    name: 'peter',
    age: 27
}

// bad
const person = student['name']

// good
const person = student.name
```

## 3.27 当通过变量访问属性时使用中括号[ ]
```javascript
const student = {
    name: 'peter',
    age: 27
}

function getName(name) {
    return student[name]
}

const name = getName('name')
```

## 3.28 优先使用 === 和 !== 而不是 == 和 !=
## 3.29 使用简写
```javascript
// bad 
if (name !== '') {

}

// good
if (name) {

}

// bad
if (name.length > 0) {

}

// good
if (name.length) {

}
```

## 3.30 行首逗号，不需要
```javascript
// bad
const story = [
    once
    , upon
    , aTime
]

// good    
const story = [
    once,
    upon,
    aTime,
]

// bad
const hero = {
    firstName: 'Ada'
    , lastName: 'Lovelace'
    , birthYear: 1815
    , superPower: 'computers'
}

// good
const hero = {
    firstName: 'Ada',
    lastName: 'Lovelace',
    birthYear: 1815,
    superPower: 'computers',
}
```

## 3.21 文件名小写
## 3.22 字符串超过一屏应该使用字符串连接号换行
```javascript
// bad
const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.'

// bad
const errorMessage = 'This is a super long error that was thrown because \
of Batman. When you stop to think about how Batman had anything to do \
with this, you would get nowhere \
fast.'

// good
const errorMessage = 'This is a super long error that was thrown because ' +
    'of Batman. When you stop to think about how Batman had anything to do ' +
    'with this, you would get nowhere fast.'
```

## 3.23 一行不要超过一屏
## 3.24 一个函数不要超过一屏

## 3.25 程序化生成字符串时，使用模板字符串代替字符串连接
```javascript
// bad
function sayHi(name) {
    return 'How are you, ' + name + '?'
}

// bad
function sayHi(name) {
    return ['How are you, ', name, '?'].join()
}

// good
function sayHi(name) {
    return `How are you, ${name}?`
}
```

## 3.26 永远不要在一个非函数代码块（if、while等）中声明一个函数，把那个函数赋一个变量
```javascript
// bad
if (currentUser) {
    function test() {
        console.log('Nope.')
    }
}

// good
let test
if (currentUser) {
    test = () => {
        console.log('Yup.')
    }
}
```

## 3.27 不要使用 arguments。可以选择 rest 语法 ... 替代
```javascript
// bad
function concatenateAll() {
    const args = Array.prototype.slice.call(arguments)
    return args.join('')
}

// good
function concatenateAll(...args) {
    return args.join('')
}
```

## 3.28 直接给函数的参数指定默认值，不要使用一个变化的函数参数
```javascript
// really bad
function handleThings(opts) {
    // 不！我们不应该改变函数参数。
    // 更加糟糕: 如果参数 opts 是 false 的话，它就会被设定为一个对象。
    // 但这样的写法会造成一些 Bugs。
    //（译注：例如当opts被赋值为空字符串，opts仍然会被下一行代码设定为一个空对象。）
    opts = opts || {}
    // ...
}

// still bad
function handleThings(opts) {
    if (opts === void 0) {
        opts = {}
    }
    // ...
}

// good
function handleThings(opts = {}) {
    // ...
}
```

## 3.29 当你必须使用函数表达式（或传递一个匿名函数）时，使用箭头函数符号
```javascript
// bad
[1, 2, 3].map(function (x) {
    return x * x
})

// good
[1, 2, 3].map((x) => {
    return x * x
})
```

## 3.30 如果一个函数适合用一行写出并且只有一个参数，那就把花括号、圆括号和 return 都省略掉。如果不是，那就不要省略
```javascript
// good
[1, 2, 3].map(x => x * x)

// good
[1, 2, 3].reduce((total, n) => {
    return total + n
}, 0)
```

## 3.31 不要使用 iterators。使用高阶函数例如 map() 和 reduce() 替代 for-of
```javascript
const numbers = [1, 2, 3, 4, 5]

// bad
let sum = 0
for (let num of numbers) {
    sum += num
}

sum === 15

// good
let sum = 0
numbers.forEach((num) => sum += num)
sum === 15

// best (use the functional force)
const sum = numbers.reduce((total, num) => total + num, 0)
sum === 15
```

## 3.32 不要使用 generators
## 3.33 条件表达式例如 if 语句通过抽象方法 ToBoolean 强制计算它们的表达式并且总是遵守下面的规则：
- 对象 被计算为 true
- Undefined 被计算为 false
- Null 被计算为 false
- 布尔值 被计算为 布尔的值
- 数字 如果是 +0、-0、或 NaN 被计算为 false, 否则为 true
- 字符串 如果是空字符串 '' 被计算为 false，否则为 true

## 3.34 使用大括号包裹所有的多行代码块
```javascript
// bad
if (test)
    return false

// good
if (test) return false

// good
if (test) {
    return false
}

// bad
function() { return false }

// good
function() {
    return false
}
```

## 3.35 使用 /\*\* ... */ 作为多行注释。包含描述、指定所有参数和返回值的类型和值
```javascript
// bad
// make() returns a new element
// based on the passed in tag name
//
// @param {String} tag
// @return {Element} element
function make(tag) {
    // ...stuff...
    return element
}

// good
/**
 * make() returns a new element
 * based on the passed in tag name
 *
 * @param {String} tag
 * @return {Element} element
 */
function make(tag) {
    // ...stuff...
    return element
}
```

## 3.36 使用 // 作为单行注释。在评论对象上面另起一行使用单行注释。在注释前插入空行
```javascript
// bad
const active = true  // is current tab

// good
// is current tab
const active = true

// bad
function getType() {
    console.log('fetching type...')
    // set the default type to 'no type'
    const type = this._type || 'no type'

    return type;
}

// good
function getType() {
    console.log('fetching type...')

    // set the default type to 'no type'
    const type = this._type || 'no type'

    return type
}
```

## 3.37 给注释增加 FIXME 或 TODO 的前缀可以帮助其他开发者快速了解这是一个需要复查的问题，或是给需要实现的功能提供一个解决方式。这将有别于常见的注释，因为它们是可操作的。使用 FIXME -- need to figure this out 或者 TODO -- need to implement
## 3.38 使用 // FIXME: 标注问题
```javascript
class Calculator {
    constructor() {
        // FIXME: shouldn't use a global here
        total = 0
    }
}
```

## 3.39 使用 // TODO: 标注问题的解决方式
```javascript
class Calculator {
    constructor() {
        // TODO: total should be configurable by an options param
        this.total = 0
    }
}
```

## 3.40 在花括号前放一个空格
```javascript
// bad
function test(){
    console.log('test')
}

// good
function test() {
    console.log('test')
}

// bad
dog.set('attr',{
    age: '1 year',
    breed: 'Bernese Mountain Dog',
})

// good
dog.set('attr', {
    age: '1 year',
    breed: 'Bernese Mountain Dog',
})
```

## 3.41 在控制语句（if、while 等）的小括号前放一个空格。在函数调用及声明中，不在函数的参数列表前加空格
```javascript
// bad
if(isJedi) {
    fight()
}

// good
if (isJedi) {
    fight()
}

// bad
function fight () {
    console.log('Swooosh!')
}

// good
function fight() {
    console.log('Swooosh!')
}
```

## 3.42 使用空格把运算符隔开
```javascript
// bad
const x = y+5

// good
const x = y + 5
```

## 3.43 在块末和新语句前插入空行
```javascript
// bad
if (foo) {
    return bar
}
return baz

// good
if (foo) {
    return bar
}

return baz

// bad
const obj = {
  foo() { },
  bar() { },
}
return obj

// good
const obj = {
  foo() { },

  bar() { },
}

return obj
```

```javascript
class XXXX {
    // good
    public static async userList1(args: any, ctx: any): Promise<any> {
        const { start, length } = args

        const info: LoginInfo = super.getLoginInfo(ctx)
        if (!info.isRoot() && !info.isAdmin())
            return super.NotAcceptable("没有权限")

        validateCgi({ start: start, length: length }, UsersValidator.findAll)

        let { cursor, limit } = getPageCount(start, length)

        let check = checkCursorLimit(cursor, limit)
        if (check)
            return super.BadRequest("参数不合要求")

        let users = await Users.getInstance().getAll(cursor, limit)
        if (users.length > 0) {
            users.map(async r => {
                r.created = gettime.getDateStr(r.created)
                r.modified = gettime.getTimeStr(r.modified)
                delete r.password
                delete r.salt
            })
        }

        return users
    }

    // bad 没有空行，难以阅读
    public static async userList2(args: any, ctx: any): Promise<any> {
        const { start, length } = args
        const info: LoginInfo = super.getLoginInfo(ctx)
        if (!info.isRoot() && !info.isAdmin())
            return super.NotAcceptable("没有权限")
        validateCgi({ start: start, length: length }, UsersValidator.findAll)
        let { cursor, limit } = getPageCount(start, length)
        let check = checkCursorLimit(cursor, limit)
        if (check)
            return super.BadRequest("参数不合要求")
        let users = await Users.getInstance().getAll(cursor, limit)
        if (users.length > 0) {
            users.map(async r => {
                r.created = gettime.getDateStr(r.created)
                r.modified = gettime.getTimeStr(r.modified)
                delete r.password
                delete r.salt
            })
        }
        return users
    }

    // bad 空行太多
    public static async userList3(args: any, ctx: any): Promise<any> {
        const { start, length } = args


        const info: LoginInfo = super.getLoginInfo(ctx)
        if (!info.isRoot() && !info.isAdmin())
            return super.NotAcceptable("没有权限")


        validateCgi({ start: start, length: length }, UsersValidator.findAll)


        let { cursor, limit } = getPageCount(start, length)


        let check = checkCursorLimit(cursor, limit)
        if (check)
            return super.BadRequest("参数不合要求")


        let users = await Users.getInstance().getAll(cursor, limit)
        if (users.length > 0) {
            users.map(async r => {
                r.created = gettime.getDateStr(r.created)
                r.modified = gettime.getTimeStr(r.modified)
                delete r.password
                delete r.salt
           })
       }


       return users
   }
}
```

## 3.43 建议使用vscode作为IDE, 上术大部分风格问题都可以自动修正

vscode基本选项参数

```javascript
{
    "files.trimTrailingWhitespace": true,   // 去除行末空白
    "editor.formatOnSave": true,            // 保存时格式化
    "editor.formatOnPaste": true            // 粘贴时格式化
}
```


# 4 其他
## 4.1 统一使用koa作为node.js的web服务框架，对cgi的参数进行白盒测试
## 4.2 时间库统一使用moment.js
