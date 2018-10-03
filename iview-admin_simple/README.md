|--src
    |--dao 【`接口层`,用来存储各个模块的接口地址】
        |--base
            |--storeManger.js
        |--erp
        |--order
    |--api 【`服务层`,用来处理各个模块的接口】
        |--base
            |--getBaseServers.js
        |--erp
        |--order
    |--components 【`通用组件层`,用来封装一些共用组件】
    |--util 【`工具层`,主要书写一些公用的方法】
        |--http.js 请求封装
        |--utils.js 公共方法
    |--config 【`公共配置层`,用来存储一些公用的配置信息】
    |--router 【`路由层`,用来配置路由信息】
        |--routers.js 配置详细路由信息
        |--index.js 使用导航守卫判断路由跳转
    |--store 【`状态管理层`】
        |--modules
            |--app.js
            |--user.js
            |--...
        |--index.js
    |--mock 【`数据模拟层`,主要是为了方便和后台并行开发】
    |--view 【`视图层`,主要是页面级的开发】
    |--assets 【`静态资源层`,主要存放一些用到的图片,icon字体资源】
        |--icon
        |--images
        |--fonts
