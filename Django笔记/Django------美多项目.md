# Django---项目

+ 一级分类-----二级分类

```python
#-------------渲染一级分类的APIView接口------------
class Indexview(APIView):
    def get(self,request):
        mes = {}
        # 在表里查询要展示页面的条件
        onecate = Onecate.objects.filter(is_show=True,is_commend=True)
        # 将查到的数据序列化转成json字符串
        one_ser = OnecateModelSerializer(onecate,many=True)

        # -------赋值一个空列表
        alist = []
        # -------遍历一级分类，填充list
        for i in onecate:
            # -----------对一级分类的值进行数据转换
            i_data = {'id':i.id,'name':i.name,'image_url':i.image_url}

            # -----------二级分类查询,有外键关联所以根据外键进行过滤查询
            twocate = Twocate.objects.filter(onecate_id=i.id).all()
            # -----------对二级分类进行序列化转换
            two_ser = TwocateModelSerializer(twocate,many=True)
            i_data['one_list'] = two_ser.data
         
            # 渲染二级分类下的产品
            goods = Goods.objects.filter(cate_list__in = twocate).distinct()
            goods_ser = GoodsModelSerializer(goods,many=True)
            i_data['goodslist'] = goods_ser.data

            # ----------添加到list
            alist.append(i_data)
        # 返回状态码vue接收
        mes['code']=200
        mes['message'] = alist
        return JsonResponse(mes)
```

+ 修改密码

```python
# 修改个人信息密码
class Change_pass(APIView):
    def post(self,request):
        mes={}
        name = request.data.get('name')
        password = request.data.get('password')
        password1 = request.data.get('password1')
        password2 = request.data.get('password2')
        user = User.objects.filter(name=name).first()
        if not all([password,password1,password2]):
            mes['code']=10020
            mes['message']='请输入完整信息'
        else:
            if check_password(password,user.password):
                if password1==password2:
                    user.password = make_password(password1)
                    mes['code']=200
                    mes['message']='修改成功'
                else:
                    mes['code']=20020
                    mes['message']='俩次密码输入不一致'
            else:
                mes['code']=20030
                mes['message']='密码错误'
            user.save()
        return JsonResponse(mes)
```

+ 从添加到购物车到操作购物车页面

  ```python
  # 添加到购物车表------------添加购物车（需设计一张购物车表，需要用户id，商品id，数量即可）
  class Get_buy(APIView):
      def post(self,request):
          mes = {}
          cart_ser = CartModelSerializer(data=request.data)
          if cart_ser.is_valid():
              cart_ser.save()
          else:
              mes['code']=20060
              mes['message']='添加失败'
          mes['code']=200
          mes['message']='添加成功'
          return JsonResponse(mes)
  #vue页面
  methods:{
  			add:function () {
  				this.number += 1;
  			},
  			minus:function () {
  				if(this.number==1){
  					alert('不能再减啦')
  				}else {
  					this.number -=1
  				}
  			},
  			buy:function () {
  				if(this.user_id){
  					var data = {'user_id':this.user_id,'goods_id':this.goods_id,'number':this.number};
  					send('/api/web/get_buy/',data,'post').then((res)=>{
  						alert('购买成功')
  						this.$router.push({'path':'cart'})
  					}).catch((err)=>{
  						console.log(err)
  					})
  				}else {
  					this.$router.push({'path':'login'})
  				}
  
  			}
  		}
  ```

+ 展示购物车列表渲染页面

  ```python
  # 根据用户id查询（谁购买的商品）---因为购物车的只有外建字段我们可以通过序列化方法获取到
  # 展示购物车列表页
  class MycartModelSerializer(serializers.ModelSerializer):
      is_checked = serializers.BooleanField(default=True)
      name = serializers.CharField(source='goods_id.name')
      image_url = serializers.CharField(source='goods_id.image_url')
      price = serializers.CharField(source='goods_id.price')
  
      class Meta:
          model = Cart
          fields = '__all__'
  
  # 展示购物车列表        
  class Show_buy(APIView):
      def post(self,request):
          mes={}
          id = request.data.get('user_id')
          print(id)
          cart = Cart.objects.filter(user_id=id)
          cart_ser = MycartModelSerializer(cart,many=True)
          mes['code']=200
          mes['message']=cart_ser.data
          return JsonResponse(mes)
      
  
  
  # 购物车加减操作表----------需要（用户id，商品id，购物车id）----因为商品必须符合三个条件才能修改
  class Change_cart(APIView):
      def post(self,request):
          mes={}
          id = request.data.get('id')
          cart = Cart.objects.filter(id=id).first()
          cart_ser = CartModelSerializer(cart,data=request.data,many=False)
          if cart_ser.is_valid():
              //is_valid() 自验证（非空等等，功能）
              cart_ser.save()
          else:
              mes['code']=20050
              mes['message']='操作失败'
          mes['code']=200
          mes['message']='操作成功'
          return JsonResponse(mes)
  #vue页面
  		computed:{
  			//所有的小计加一起
  			totalprice:function () {
  				//设置变量接收
  				var total = 0;
  				for(var i=0;i<this.cartlist.length;i++){
  					//用i作为下标查询
  					var temp = this.cartlist[i];
  					if(temp.is_checked){
  						var xiaoji = temp.price*temp.number;
  						total += xiaoji
  					}
  
  				}
  				return total
  
  			},
  			totalnumber:function () {
  				var totalnumber = 0;
  				for(var c=0;c<this.cartlist.length;c++){
  					var temp = this.cartlist[c];
  					if(temp.is_checked){
  						totalnumber += temp.number
  					}
  
  				}
  				return totalnumber
  			},
  			totalcount:function () {
  				var totalcount = 0;
  				for(var i=0;i<this.cartlist.length;i++){
  					var temp = this.cartlist[i];
  					if(temp.is_checked){
  						totalcount += temp.number
  					}
  				}
  				return totalcount
  			}
  
  		},
  		methods:{
              // +  操作数据库
  			add:function (c) {
  				var data = {'id':c.id,'user_id':this.user_id,'goods_id':c.goods_id,'number':(c.number+1)};
  				this.axios({
  					url:'/api/web/change_cart/',
  					method:'post',
  					data:data
  				}).then((res)=>{
  					var data = res.data
  					if(data.code==200){
  						c.number+=1
  					}
  				}).catch((err)=>{
  					console.log(err)
  				})
  
  			},
              // -  操作数据库（传三个id，跟数量，然后存入数据库）
  			mis:function (c) {
  				var data = {'id':c.id,'user_id':this.user_id,'goods_id':c.goods_id,'number':(c.number-1)};
  				this.axios({
  					url:'/api/web/change_cart/',
  					method:'post',
  					data:data
  				}).then((res)=>{
  					if(res.data.code==200){
  						if(c.number>1){
  					c.number -= 1
  				}else {
  					alert('不能再减啦')
  				}
  					}
  				}).catch((err)=>{
  					console.log(err)
  				})
  
  			},
  			//删除购物车表中的商品
  			pop_cart:function (id) {
  				this.axios({
  					url:'/api/web/pop_cart/?id='+id,
  					method:'get',
  					data:{}
  				}).then((res)=>{
  					console.log(res);
  					if(res.data.code==200){
  						alert(res.data.message);
  						this.$router.go()
  					}
  				}).catch((err)=>{
  					console.log(err)
  				})
  			}
  		},
  ```

  

## 订单表设计

```
class Order(Base,model.models):
```

+ 订单表

  创建时间-----用户外建-----订单号（唯一）-----商品数量-----商品总价-----

  购买总数量-----支付方式----- 第三方支付编号-----订单状态-----收货地址-----运费

+ 订单商品表

  商品外建-----订单外建-----商品价格-----商品数量-----商品评论