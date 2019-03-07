# API 参考

::: tip
在 Vue 实例内部，你可以通过 `$routerTab` 访问路由页签实例。因此你可以调用 `vm.$routerTab.close()`。
:::

## RouterTab Props

### `alive-key`

页面组件缓存的键

- 类型: `string | Function`

  - 如果类型为 `string` ，则取 `$route[aliveKey]` 的值

  - 如果类型为 `Function` ，则取 `aliveKey($route)` 返回的字符串。该函数不应返回随机变化的字符串，以免页签无法与缓存的页面对应

- 默认值: `'path'`
  
  根据 `$route.path` 来缓存页面组件。

  - 同一路由-不同 `$route.params` 的页面，各自打开独立的页签，单独缓存

  - 同一路由-相同 `$route.params` -不同 `$route.query` 的页面，共用同一个页签，后打开的页面将会替换之前页签内的页面，并且旧的页面缓存也被清除

  - 仅仅 `$route.hash` 不同的页面，共用同一页签和缓存

- 示例：

  ``` html
  <!-- 取 $route.fullPath -->
  <router-tab alive-key="fullPath"></router-tab>

  <!-- 函数方式 -->
  <router-tab :alive-key="route => route.fullPath + '1'"></router-tab>
  ```

### `i18n`

语言配置

- 类型: `string | Object`

  - 如果类型为 `string` ，可以设置为内置的语言 `'zh-CN'` (默认) 和 `'en'`

  - 如果类型为 `Object` ，可设置自定义的语言

- 默认值: `'zh-CN'`

- 示例：

  - **指定内置语言**

    ``` html
    <router-tab i18n="en"></router-tab>
    ```

  - **自定义语言**

    ``` html
    <router-tab :i18n="lang"></router-tab>
    ```

    ``` javascript
    export default {
      data () {
        return {
          lang: {
            tab: {
              untitled: 'Untitled Page'
            },
            contextmenu: {
              refresh: 'Refresh This',
              refreshAll: 'Refresh All',
              close: 'Close This',
              closeLefts: 'Close to the Left',
              closeRights: 'Close to the Right',
              closeOthers: 'Close Others'
            },
            msg: {
              keepOneTab: 'Keep at least 1 tab'
            }
          }
        }
      }
    }
    ```

### `tabs`

**初始页签数据**，进入页面时默认显示的页签。相同 `aliveKey` 的页签只保留第一个

- 类型: `Array <string | Object>`
  
  - tabs子元素类型为 `string` 时，应配置为要打开页面的 `fullPath` ，页签的标题/图片/提示等信息会从对应页面的 `router` 配置中获取

  - tabs子元素类型为 `Object` 时：
    
    - to: 页签路由地址，跟 `router.push` 的 `location` 参数一致，可以为 `fullPath`，也可以为 `location` 对象 - [参考文档](https://router.vuejs.org/zh/guide/essentials/navigation.html#router-push-location-oncomplete-onabort)
    
    - title: 页签标题，如果页面有设置 `routerTab.title` 动态标题，可在此设置最终的动态标题值，以免与默认从 `router` 获取的标题不一致
    
    - closable: 页签是否允许关闭，默认为 `true`

- 默认值: `[]`

### `router-view`

**Vue Router Tab 内置 `<router-view>` 组件的配置**

- 类型: `Object`
  
  配置参考: [Vue Router - \<router-view\> Props](https://router.vuejs.org/zh/api/#router-view-props)

- 默认值: `{}`



### `tab-transition`

**页签过渡效果**，新增和关闭页签时的过渡

- 类型: `string | Object`

  - 类型为 `string` 时，应配置为 `transition.name`

  - 类型为 `Object` 时，配置参考: [Vue - transition](https://cn.vuejs.org/v2/api/#transition)

- 默认值: `'router-tab-zoom-lb'`

- 示例：

  ``` html
  <!-- 直接配置过渡名称 -->
  <router-tab tab-transition="my-transition"></router-tab>

  <!-- 过渡详细配置 -->
  <router-tab :tab-transition="{ name: 'my-transition', 'enter-class': 'my-transition-enter' }"></router-tab>
  ```


### `page-transition`

**页面过渡效果**

- 类型: `string | Object`
  
  同 [`tab-transition`](#tab-transition)

- 默认值: `{
  name: 'router-tab-swap',
  mode: 'out-in'
}`


## RouterTab 插槽

### 页签项

- **示例：**
  ``` html
  <router-tab>
    <template v-slot="{ tab: { id, title, icon, closable }, tabs, index}">
      <i v-if="icon" class="tab-icon" :class="icon"></i>
      <span class="tab-index">{{index}}</span>
      <span class="tab-title">{{title || '未命名页签'}}</span>
      <i class="tab-close el-icon-close" v-if="closable !== false &&tabs.length > 1" @click.prevent="close(id)"></i>
    </template>
  </router-tab>
  ```

## RouterTab 实例属性

### `routerTab.activedTab`

当前激活的页签id


## RouterTab 实例方法


### `routerTab.close`

- **参数**：
  - `{string | Object} [location]` 路由地址 - [参考文档](https://router.vuejs.org/zh/guide/essentials/navigation.html#router-push-location-oncomplete-onabort)
  - `{Boolean} [fullMatch = true]` 是否全匹配（匹配fullPath去除hash部分）

- **说明**：

  关闭指定 `location` 的页签

- **示例**：

  ``` js
  // 如果未提供 `location`，则默认关闭当前激活的页签
  vm.$routerTab.close()

  // 关闭指定 fullPath 的页签
  vm.$routerTab.close('/page/1')

  // 关闭指定 location 的页签
  vm.$routerTab.close({
    name: 'page',
    params: {
      id: 2
    }
  })
  
  // 关闭与给定地址相同 aliveKey 的页签，即使地址不完全匹配
  vm.$routerTab.close('/page/1', false)
  ```

  
### `routerTab.refresh`

- **参数**：
  - `{string | Object} [location]` 路由地址 - [参考文档](https://router.vuejs.org/zh/guide/essentials/navigation.html#router-push-location-oncomplete-onabort)
  - `{Boolean} [fullMatch = true]` 是否全匹配（匹配fullPath去除hash部分）

- **说明**：

  刷新指定 `location` 的页签

- **示例**：

  ``` js
  // 如果未提供 `location`，则默认刷新当前激活的页签
  vm.$routerTab.refresh()

  // 刷新指定 fullPath 的页签
  vm.$routerTab.refresh('/page/1')

  // 刷新指定 location 的页签
  vm.$routerTab.refresh({
    name: 'page',
    params: {
      id: 2
    }
  })
  
  // 刷新与给定地址相同 aliveKey 的页签，即使地址不完全匹配
  vm.$routerTab.refresh('/page/1', false)
  ```

### `routerTab.refreshAll`

- **参数**：
  - `{Boolean} [force = false]` 如果 `force` 为 `true`，则忽略页面组件的 `beforePageLeave` 配置，强制刷新所有页签

- **说明**：

  刷新所有页签

- **示例**：

  ``` js
  // 刷新所有页签
  vm.routerTab.refreshAll()

  // 强制刷新所有页签
  vm.routerTab.refreshAll(true)
  ```

## Route.meta 路由元

### `meta.title`

- 类型: `string`
- 默认值: `'新页签'`

页签标题



### `meta.icon`

- 类型: `string`

页签图标


### `meta.tips`

- 类型: `string`
- 默认值: 默认和页签标题 `meta.title` 保持一致

页签提示


### `meta.aliveKey`

页面组件缓存的键，用以设置路由独立的页签缓存规则。

配置参考: [RouterTab Props > alive-key](#alive-key)