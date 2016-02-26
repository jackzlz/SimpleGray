---
layout: post
title: 用ReactJS写的简单组件Pagebar（待完善。。。）
category: 技术
---

用ReactJS写的简单组件Pagebar（待完善。。。）

~~~ html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>react pagebar</title>
    <link rel="stylesheet" type="text/css" href="css/bootstrap.min.css" />
    <script type="text/javascript" src="js/react.js"></script>
    <script type="text/javascript" src="js/JSXTransformer.js"></script>
</head>
<body>
    <div id="pagebar"></div>
    <script type="text/jsx">
    var PageBar = React.createClass({
        handleClick:function(index) {
            this.setState({currPage: index});
        },
        handlePrev:function() {
            var currPage = this.state.currPage-1;
            if (currPage < 1) {
                return;
            }
            this.setState({currPage:currPage});
        },
        handleNext:function(){
            var currPage = this.state.currPage+1;
            if (currPage > this.props.totalCount) {
                return;
            }
            this.setState({currPage:currPage});
        },

        getInitialState: function(){
            return {currPage:1};
        },

        render : function() {
            var lis = [];
            var liClassName;
            for (var i = 0; i < this.props.totalCount; i++) {
                liClassName = "page-nav";
                if ((i+1) === this.state.currPage) {
                    liClassName = "page-nav active"
                }
                lis.push(<li className={liClassName}><a href="javascript:void(0);" onClick={this.handleClick.bind(this, i+1)}>{i+1}</a></li>);
            }
            return (
                <nav>
                    <ul className="pagination" style={{margin:0}}>
                        <li className="page-prev">
                            <a href="javascript:void(0);" aria-label="Previous" onClick={this.handlePrev}>
                                <span aria-hidden="true">&laquo;</span>
                            </a>
                        </li>
                        {lis}
                        <li className="page-next">
                            <a href="javascript:void(0);" aria-label="Next" onClick={this.handleNext}>
                                <span aria-hidden="true">&raquo;</span>
                            </a>
                        </li>
                    </ul>
                </nav>
            );
        }
    });

    React.render(<PageBar totalCount="10" />, document.getElementById("pagebar"));
    </script>
</body>
</html>
~~~

<!--more-->

效果图

![pagebar](http://7xqzc5.com1.z0.glb.clouddn.com/23185255_IV00.gif)
