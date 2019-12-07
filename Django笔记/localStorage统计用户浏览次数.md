# localStorage统计用户浏览次数

+ 在vue页面中加载

  ```python
  mounted() {
      		#如果在页面刷新的次数显示 NaN，则先存入count次数，localStorage.setItem('count',0)
      		localStorage.setItem('count',0)        #存成数字类型，之后再执行下面的
      		#声明count变量，从localStorage中获取count
              var count = localStorage.getItem('count')
      		#判断如果为空或者为空字符串
              if(count==null || count== ""){
              #条件满足后，默认给localStorage存入 1 
                  localStorage.setItem('count',1)
              }else {
              # 每次浏览时count累加
                  count = parseInt(count) + 1
              #累加后的count存入localStorage
                  localStorage.setItem('count',count)
              }
      		#最后把count 赋值给一个变量，用{{}}形式显示出来
              this.showcount = count
        }
  ```

  
  
   

