#LittleGL 5.0.2 API 说明

## 1. obj

基本概念: 
LittleGL中所有对象都是继承与obj对象; 

obj对象具有以下属性: 
>坐标(Coordinates)
>父对象(Parent object)
>子对象们(Children)
>风格(Style)

以下属性也是obj的属性(有些对象可能是没有以下属性的):
>隐藏(hidden)
>点击(click)
>置顶(top)
>拖拽(drag)：长按设备后可以拖拽对象	
>抛出(drag_throw)：拖拽过程中，可以抛出该对象
>父对象一起拖拽(drag_parent)：当对象被拖拽时，父对象也会被移动

窗口对象拥有的属性(实际测试出来的)：
>隐藏(hidden)：有
>点击(click)：无
>置顶(top)：
>拖拽(drag)：无
>抛出(drag_throw)：无
>父对象一起拖拽(drag_parent)：无

LIttleGL初始化之后(lv_init()函数调用之后)会创建3个屏幕：
1. 默认屏幕(def_scr)
2. 顶层屏幕(top_layer)
3. 系统屏幕(sys_layer), 系统层是系统库中使用的, 例如鼠标

1.1. 创建基础对象 
```
lv_obj_t * lv_obj_create(lv_obj_t * parent, lv_obj_t * copy); 
```
>screen1 = lv_obj_create(NULL, NULL)：创建屏幕(screen)，创建屏幕后可以使用lv_scr_load(screen1)加载屏幕(screen).

1.2.  删除对象和它的所有子对象;	
```
lv_res_t lv_obj_del(lv_obj_t * obj);
```

说明: 返回值为LV_RES_INV对象被删除. 
    	
1.3. 删除所有的子对象	
```
void lv_obj_clean(lv_obj_t *obj);
```

1.4. 将对象标记为无效，因此其当前位置将被“lv_refr_task”重绘	
```
void lv_obj_invalidate(lv_obj_t * obj);
```

1.5.加载屏幕对象	
```
void lv_scr_load(lv_obj_t * scr);
```

1.6.为对象设置一个新的父对象。相对位置不变。	
```
void lv_obj_set_parent(lv_obj_t * obj, lv_obj_t * parent);
```

1.7.设置对象的相对位置(相对于父对象)	
```
void lv_obj_set_pos(lv_obj_t * obj, lv_coord_t x, lv_coord_t y);
```

1.8. 设置对象的x坐标(相对于父对象)	
```
void lv_obj_set_x(lv_obj_t * obj, lv_coord_t x);
```

1.9.设置对象的y坐标(相对于父对象)	
```
void lv_obj_set_y(lv_obj_t * obj, lv_coord_t y);
```

1.10.设置对象的大小	
```
void lv_obj_set_size(lv_obj_t * obj, lv_coord_t w, lv_coord_t h);
```

1.11.设置对象的宽度	
```
void lv_obj_set_width(lv_obj_t * obj, lv_coord_t w); 
```

1.12.设置对象的高度	
```
void lv_obj_set_height(lv_obj_t * obj, lv_coord_t h);
```

1.13.将对象对齐到另一个对象	
```
void lv_obj_align(lv_obj_t * obj,lv_obj_t * base, lv_align_t align, lv_coord_t x_mod, lv_coord_t y_mod);
```
>1. `lv_obj_t * obj`：对齐对象
>2. `lv_obj_t * base`：参考对象, NULL代表参考对象为对齐对象的父对象
>3. `lv_align_t align`： 对齐参数如下：LV_ALIGIN_(OUT/IN)_(TOP/LEFT/RIGHT/BOTTOM)_(TOP/LEFT/RIGHT/BOTTOM)
![对齐参数](https://littlevgl.com/docs/object_types/align.png  "对齐参数")
>4. `lv_coord_t x_mod, lv_coord_t y_mod`: x和y偏移量

1.14.设置对象的风格	
```
void lv_obj_set_style(lv_obj_t * obj, lv_style_t * style);
```

You can set a new style for an object with the lv_obj_set_style(obj, &new_style) function. If NULL is set as style then the object will inherit its parent's style. If you modify a style you have to notify the objects who are using the modified styled. You can use either lv_obj_refresh_style(obj) or to notify all object with a given style lv_obj_report_style_mod(&style). Set lv_obj_report_style_mod's parameter to NULL to notify all objects. 后面的刷新是什么意思????

>对象继承父对象的风格：lv_obj_set_style(obj, NULL); 


下面代码有问题: 点击后风格会消失
~~~
lv_res_t action1(struct _lv_obj_t * obj)
{
    lv_obj_set_parent(label, btn2); 
}

lv_res_t action2(struct _lv_obj_t * obj)
{
    lv_obj_set_parent(label, btn1); 
}

int littlevGl(void)
{
    lv_theme_t* theme = lv_theme_material_init(22, NULL); 
    lv_theme_set_current(theme); 
    
    lv_obj_t* win = lv_win_create(lv_scr_act(), NULL); 
    lv_win_set_title(win, "LittleGl 5.0.2"); 
    lv_win_set_btn_size(win, 32); 
    lv_obj_set_drag(win, true);                     /* window无法被拖拽 */
    // lv_obj_set_drag_parent(win, true); 
    // lv_obj_set_hidden(win, true);                /* 隐藏窗口 */
    // lv_obj_del(win);                             /* 删除obj和其子对象 */
    // lv_obj_clean(win);                           /* 删除其子对象 */

    btn1 = lv_btn_create(win, NULL); 
    btn2 = lv_btn_create(win, NULL); 
    lv_btn_set_action(btn1, LV_BTN_ACTION_CLICK, action1); 
    lv_btn_set_action(btn2, LV_BTN_ACTION_CLICK, action2);
    lv_obj_align(btn2, btn1, LV_ALIGN_OUT_RIGHT_MID, 10, 0); 
    lv_obj_set_style(btn1, &lv_style_pretty_color);
    lv_obj_set_drag(btn2, true); 

    label = lv_label_create(btn1, NULL); 
    lv_label_set_text(label, "Hello"); 

    lv_win_add_btn(win, SYMBOL_CLOSE,     NULL); 
    lv_win_add_btn(win, SYMBOL_UPLOAD,    NULL); 
    lv_win_add_btn(win, SYMBOL_BLUETOOTH, NULL); 

    return 0;
~~~

1.15. 通知对象其样式被修改(什么情况下用???)	
```
void lv_obj_refresh_style(lv_obj_t * obj);
```

1.16. 如果样式被修改，通知所有对象(什么情况下用???)	
```
void lv_obj_report_style_mod(lv_style_t * style);
```

1.17. 隐藏对象。它不会是可见的和可点击的	
```
void lv_obj_set_hidden(lv_obj_t * obj, bool en);
```

1.18. 启用或禁用点击对象	
```
void lv_obj_set_click(lv_obj_t * obj, bool en);
```

说明: win对象无法启动点击

1.19. 对象置顶	
```
void lv_obj_set_top(lv_obj_t * obj, bool en)
```

下面测试代码无法将btn1置顶?有可能是对函数理解错误
～～～
lv_obj_t *btn1; 
lv_obj_t *btn2; 
lv_obj_t *label; 

int littlevGl(void)
{
    lv_obj_t* win = lv_win_create(lv_scr_act(), NULL); 
    lv_win_set_title(win, "LittleGl 5.0.2"); 
    lv_win_set_btn_size(win, 32); 
    lv_win_add_btn(win, SYMBOL_CLOSE,     NULL); 
    lv_win_add_btn(win, SYMBOL_UPLOAD,    NULL); 
    lv_win_add_btn(win, SYMBOL_BLUETOOTH, NULL); 

    btn1 = lv_btn_create(win, NULL); 
    btn2 = lv_btn_create(win, NULL); 
    lv_obj_t *l1 = lv_label_create(btn1, NULL); 
    lv_obj_t *l2 = lv_label_create(btn2, NULL);

    lv_label_set_text(l1, "btn1"); 
    lv_label_set_text(l2, "btn2"); 

    // lv_obj_align(btn2, btn1, LV_ALIGN_OUT_RIGHT_MID, -10, 0); 
    lv_obj_set_style(btn2, &lv_style_btn_rel); 


    // lv_obj_t* s1 = lv_scr_act(); 
    // lv_obj_t* s2 = lv_obj_create(NULL, NULL); 
    // lv_scr_load(s2);

    // lv_obj_set_top(btn1, true); 
    lv_obj_set_top(btn2, true); 

    return 0; 
}
～～～

1.20. 设置对象拖拽	
```
void lv_obj_set_drag(lv_obj_t * obj, bool en)
```

说明: win对象测试出来是无法设置拖拽使能的!!!

1.21.在被拖动后，允许抛出一个对象	
```
void lv_obj_set_drag_throw(lv_obj_t * obj, bool en)
```

说明: 就是拖拽过程中可以将对象甩出去

1.22.允许使用父类来拖拽相关操作(待理解作用)	
```
void lv_obj_set_drag_parent(lv_obj_t * obj, bool en)
```

说明: 暂时不理解是干什么的?

1.23.清除保护位(待理解作用)	
```
void lv_obj_set_protect(lv_obj_t * obj, uint8_t prot)
```
在库中会自动发生一些特定的动作。为了防止一个或多个这样的行为，你可以保护对象不受伤害。以下保护:
1. LV_PROTECT_NONE 不保护
2. LV_PROTECT_POS 防止自动定位(待理解！！！！)
3. LV_PROTECT_FOLLOW 在自动排序(例如在lv_cont中布局)中防止对象被跟踪
4. LV_PROTECT_PARENT 
5. LV_PROTECT_CHILD_CHG Disable the child change signal. (库内部使用)
1.24. 设置对象的信号函数(待理解作用)	
```
void lv_obj_set_signal_func(lv_obj_t * obj, lv_signal_func_t fp);
```

1.25.为对象设置一个新的设计函数(待理解作用)	
```
void lv_obj_set_design_func(lv_obj_t * obj, lv_design_func_t fp);
```

1.26.为对象分配一个新的ext数据(待理解作用)	
```
void * lv_obj_allocate_ext_attr(lv_obj_t * obj, uint16_t ext_size);
```

1.27.发送一个'LV_SIGNAL_REFR_EXT_SIZE'信号给对象(待理解作用)	
```
void lv_obj_refresh_ext_size(lv_obj_t * obj);
```

1.28. 为对象设置特定于应用程序的编号(待理解作用)	
```
void lv_obj_set_free_num(lv_obj_t * obj, LV_OBJ_FREE_NUM_TYPE free_num);
```

1.29. 为对象设置特定于应用程序的指针(待理解作用)	
```
void lv_obj_set_free_ptr(lv_obj_t * obj, void * free_p);
```

1.30. 动画的对象???(这个是纯翻译, 再理解下什么意思)	
```
void lv_obj_animate(lv_obj_t * obj, lv_anim_builtin_t type, uint16_t time, uint16_t delay, void (*cb) (lv_obj_t *));
```

1.31. 返回当前激活的屏幕	
```
lv_obj_t * lv_scr_act(void);
```

1.32.返回顶部图层。(在每个屏幕上都是一样的，它在正常的屏幕层之上)	
```
lv_obj_t * lv_layer_top(void);
```

1.33. 返回系统层。(在每个屏幕上都是一样的，它在所有其他图层之上)	
```
lv_obj_t * lv_layer_sys(void);
```

1.34. 返回对象所在的屏幕	
```
lv_obj_t * lv_obj_get_screen(lv_obj_t * obj);
```

1.35.返回对象的父类对象	
```
lv_obj_t * lv_obj_get_parent(lv_obj_t * obj);
```

 1.36.迭代对象的子对象(从最后创建的子对象开始迭代)	
```
lv_obj_t * lv_obj_get_child(lv_obj_t * obj, lv_obj_t * child);
```
>1. `lv_obj_t * obj`：待获取子对象的对象
>2. `lv_obj_t * child`：待获取的子对象前一个子对象
>3. `返回值 lv_obj_t * `：获取的子对象
例如：获取对象的最后创建的子对象:child = lv_obj_get_child(obj, NULL); 

 1.36.迭代对象的子对象(从最开始创建的子对象开始迭代)	
```
lv_obj_t * lv_obj_get_child_back(lv_obj_t * obj, lv_obj_t * child);
```
>1. `lv_obj_t * obj`：待获取子对象的对象
>2. `lv_obj_t * child`：待获取的子对象前一个子对象
>3. `返回值 lv_obj_t * `：获取的子对象
例如：获取对象的第一个创建的子对象:child = lv_obj_get_child_back(obj, NULL); 

1.37.计算对象的子对象数量	
```
uint16_t lv_obj_count_children(lv_obj_t * obj);
```

1.38. 获取对象的坐标(x1, y1, x2, y2)
```
void lv_obj_get_coords(lv_obj_t * obj, lv_area_t * cords_p);
```

1.39. 获取对象的x坐标(应该是对象的原点, 待测试)
```
lv_coord_t lv_obj_get_x(lv_obj_t * obj);
```

1.39. 获取对象的y坐标(应该是对象的原点, 待测试)
```
lv_coord_t lv_obj_get_y(lv_obj_t * obj);
```

1.39. 获取对象的宽度
```
lv_coord_t lv_obj_get_width(lv_obj_t * obj);
```

1.39. 获取对象的高度
```
lv_coord_t lv_obj_get_height(lv_obj_t * obj);
```

1.40. 获取对象的扩展大小(??????不理解)
```
lv_coord_t lv_obj_get_ext_size(lv_obj_t * obj);
```

1.41. 获取对象风格
```
lv_style_t * lv_obj_get_style(lv_obj_t * obj);
```

1.42. 获取对象是否隐藏
```
bool lv_obj_get_hidden(lv_obj_t * obj);
```

1.43. 获取对象是否可以点击
```
bool lv_obj_get_click(lv_obj_t * obj);
```

1.44. 获取对象是否被置顶
```
bool lv_obj_get_top(lv_obj_t * obj);
```

1.45. 获取对象是否可以被拖拽
```
bool lv_obj_get_drag(lv_obj_t * obj);
```

1.46. 获取对象是否可以被抛出
```
bool lv_obj_get_drag_throw(lv_obj_t * obj);
```

1.47. 获取对象是否开启了拖拽父对象一起拖拽功能
```
bool lv_obj_get_drag_parent(lv_obj_t * obj);
```
1.48. 获取对象保护属性
```
uint8_t lv_obj_get_protect(lv_obj_t * obj);
```

1.49. 判断对象的对应保护为是否开启(保护为可以多选, 通过"与"进行多选)
```
bool lv_obj_is_protected(lv_obj_t * obj, uint8_t prot);
```

1.50. 获取对象的型号函数
```
lv_signal_func_t   lv_obj_get_signal_func(lv_obj_t * obj);
```

1.51. Get the design function of an object
```
lv_design_func_t lv_obj_get_design_func(lv_obj_t * obj);
```

1.52. Get the ext pointer
```
void * lv_obj_get_ext_attr(lv_obj_t * obj);
```

1.53. Get the free number
```
LV_OBJ_FREE_NUM_TYPE lv_obj_get_free_num(lv_obj_t * obj);
```

1.54. Get the free pointer
```
void * lv_obj_get_free_ptr(lv_obj_t * obj);
```

1.55. Get the group of the object
```
void * lv_obj_get_group(lv_obj_t * obj);
```
