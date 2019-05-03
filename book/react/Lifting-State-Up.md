## React 兄弟组件通讯

无论是React还是Vue，一般`组件传值`都是`父子组件`之前互相通信传值比较多，子组件通过一个`props`来接收父组件传来的值。但是有些应用情景会涉及到兄弟组件传值。

今天说react的兄弟组件传值：
答案很简单，就是通过父组件的回调把值传给父组件，父组件拿到值后来做兄弟组件的更新。

```
//子组件
class 孩子 extends React.Component {
    constructor(props) {
        super(props)
        //绑定this
        this.handleChange = this.handleChange.bind(this)
    }

    handleChange(e) {
        //2.子组件拿到回调a 传给->爸爸（父组件）
        this.props.onHit(e.target.value)
    }


    render() {
        let sex = this.props.sex
        return (
            <div>
                <h1>我的孩子是：{sex}</h1>
                <input type="text" value={sex} onChange={this.handleChange} />
            </div>
        )
    }
}

//兄弟组件通讯 都通过爸爸中转
class 爸爸 extends React.Component {
    constructor(props) {
        super(props)
        this.a = this.a.bind(this)
        this.state = { sex: '' }
    }

    a(sex) {
        //3.爸爸接收 <-孩子(子组件)传来的值
        this.setState({
            sex: sex //3.保存到父组件state
        })
    }

    render() {
        let sex = this.state.sex
        return (
            <div>
                {/* 子组件1 */} {/* 1.把回调a传给孩子(子组件) */}
                <孩子 sex={sex} onHit={this.a} />
                {/* 子组件2 */}
                <孩子 sex={sex} onHit={this.a} />
                {/*子组件2也是绑定的变量sex 所以父组件在做第三步的时候(保存sex)也就会更新组件2*/}
            </div>
        )
    }
}

ReactDOM.render(<爸爸 />, document.getElementById('root'));
```


1.父组件把回调`a`传给子组件`onHit={this.a}`  
2.子组件通过`this.props.onHit(e.target.value)`拿到父组件的回调,并把需要传递的值`e.target.value`放在回调里传给父组件。  
3.父组件在回调`a`里拿到值后存到`state`里面，这个时候父组件就可以更新它的子组件了(子组件的兄弟组件)，这就成功的通过父组件把值传到了兄弟组件。

![react兄弟组件通讯](https://img.xuewuzhijing.top/react.jpg)
因为兄弟组件使用是同一个父组件的sex变量，所以只要一个子组件更新了父组件的sex变量，另个一个子组件(兄弟组件)当然也会更新啦。