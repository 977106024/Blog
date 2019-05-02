## React 为啥要绑定this

在react中我们总是看到事件后面跟着一个.bind(this),也就是绑定this，如果你尝试不写绑定，那react就会报错`Cannot read property 'setState' of undefined`，意思是说`this`是`undefined`。