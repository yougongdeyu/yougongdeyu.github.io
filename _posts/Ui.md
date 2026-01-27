

1.angularjs：
    module：
        注册 controller / service / directive 等
    Controller：
        处理页面行为，操作 $scope，调用 service，旧时代 MVC 中的 C。
    View：
        html+指令语法
    Directive：
        自定义 HTML 标签，Directive = AngularJS 时代的“组件”
    Service：
        
        service：
        provider：
        factory
    $Scope:
        双向绑定的核心
    DI:
        依赖注入
        app.controller('Ctrl', function($scope, $http) {});

    Module
    ├── Controller
    ├── Service / Factory
    ├── Directive
    ├── Filter
    ├── Config / Run block
    └── Router

    HTML View ←→ $scope ←→ Controller ←→ Service ←→ Backend

    angularjs 原理：
        数据模型（scope）
        依赖收集（watcher）
        脏检查循环（digest loop） 

        浏览器会执行js标签。
        <script src="angular.js"></script>

        angularjs demo：

        <!DOCTYPE html>
        <body>
        <div id="app">
            <p>{{ name }}</p>
            <button onclick="change()">Change</button>
        </div>
        ## 模版

        <script>
            class Scope {
            constructor() {
                this.$$watchers = [];
            }

            $watch(exp, fn) {
                this.$$watchers.push({ exp, fn, last: undefined });
            }

            $digest() {
                let dirty;
                do {
                dirty = false;
                for (const w of this.$$watchers) {
                    const newVal = w.exp();
                    if (newVal !== w.last) {
                    w.fn(newVal, w.last);
                    w.last = newVal;
                    dirty = true;
                    }
                }
                } while (dirty);
            }
            }

            ## compile

            function compile(root, scope) {
            const nodes = root.querySelectorAll("*");

            nodes.forEach(node => {
                if (node.childNodes.length === 1 && node.innerText.includes("{{")) {
                const exp = node.innerText.match(/{{(.*?)}}/)[1].trim();

                scope.$watch(
                    () => scope[exp],
                    (newVal) => {
                    node.innerText = newVal;
                    }
                );
                }
            });
            }

            const $scope = new Scope();
            $scope.name = "Tom";

            function change() {
            $scope.name = "Jerry";
            $scope.$digest();
            }

            compile(document.getElementById("app"), $scope);
            $scope.$digest();
        </script>
        </body>


        watchers.push({
        getter: () => scope[exp],
        listener: (newVal) => node.innerText = newVal,
        lastValue: undefined
        });
        compile 阶段解析 DOM 模板中的表达式，生成 getter 函数，并在 link 阶段通过 $watch 注册 watcher，使其在 digest 脏检查过程中能够感知数据变化并更新对应 DOM。
        scope.$watch(){watchers.push({})}

        双向绑定原理： 
            输入事件负责“写 scope”，digest + watcher 负责“从 scope 刷新 DOM”
            从 View → Model：靠 input 事件监听
            从 Model → View：靠 $watch + $digest

        Link 阶段：
            const scope = new Scope();
            scope.name = "Alice";
            const node = document.getElementById("app");
            const linkFn = compile(node);  // compile 阶段  
            linkFn(scope);                 // link 阶段

            node compile 注册 watcher
            linkFn(scope);传入具体 scope

        那么多controller 如何组织的：

        真实的angularjs如何工作的：
                $scope.$apply = function(fn) {
                    try {
                        return fn();          // 执行你写的表达式
                    } finally {
                        $rootScope.$digest(); // 调用脏检查
                    }
                };
            所有 AngularJS 指令事件内部，自动包了一层 $apply → 自动触发 digest
            比如ng-click，$http(...)，$timeout(fn, delay)，ng-model


        dispatch：
            那什么时候用事件冒泡，什么时候用事件捕获
            

        $emit：$broadcast / $on
            event.stopPropagation()
        








