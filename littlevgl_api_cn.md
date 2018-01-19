#LittleGL 5.0.2 API 说明

### 1. obj

1.1. 创建基础对象
lv_obj_t * lv_obj_create(lv_obj_t * parent, lv_obj_t * copy); 
说明: 如果parent是null则创建一个屏幕对象.

1.2.  删除对象和它的所有子对象;
lv_res_t lv_obj_del(lv_obj_t * obj);
说明: 返回值为LV_RES_INV对象被删除. 
    	
1.3. 删除所有的子对象
void lv_obj_clean(lv_obj_t *obj);

1.4. 将对象标记为无效，因此其当前位置将被“lv_refr_task”重绘
void lv_obj_invalidate(lv_obj_t * obj);
说明: 重绘是什么???

1.5.加载屏幕对象
void lv_scr_load(lv_obj_t * scr);

1.6.为对象设置一个新的父对象。相对位置不变。
void lv_obj_set_parent(lv_obj_t * obj, lv_obj_t * parent);

1.7.设置对象的相对位置(相对于父对象)
void lv_obj_set_pos(lv_obj_t * obj, lv_coord_t x, lv_coord_t y);

1.8. 设置对象的x坐标(相对于父对象)
void lv_obj_set_x(lv_obj_t * obj, lv_coord_t x);

1.9.设置对象的y坐标(相对于父对象)
void lv_obj_set_y(lv_obj_t * obj, lv_coord_t y);

1.10.设置对象的大小
void lv_obj_set_size(lv_obj_t * obj, lv_coord_t w, lv_coord_t h);

1.11.设置对象的宽度
void lv_obj_set_width(lv_obj_t * obj, lv_coord_t w); 

1.12.设置对象的高度
void lv_obj_set_height(lv_obj_t * obj, lv_coord_t h);

1.13.将对象对齐到另一个对象
void lv_obj_align(lv_obj_t * obj,lv_obj_t * base, lv_align_t align, lv_coord_t x_mod, lv_coord_t y_mod);

1.14.设置对象的风格
void lv_obj_set_style(lv_obj_t * obj, lv_style_t * style);

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
void lv_obj_refresh_style(lv_obj_t * obj);

1.16. 如果样式被修改，通知所有对象(什么情况下用???)
void lv_obj_report_style_mod(lv_style_t * style);

1.17. 隐藏对象。它不会是可见的和可点击的
void lv_obj_set_hidden(lv_obj_t * obj, bool en);

1.18. 启用或禁用点击对象
void lv_obj_set_click(lv_obj_t * obj, bool en);
说明: win对象无法启动点击

1.19. 对象置顶
void lv_obj_set_top(lv_obj_t * obj, bool en)

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
void lv_obj_set_drag(lv_obj_t * obj, bool en)
说明: win对象测试出来是无法设置拖拽使能的!!!

1.21.在被拖动后，允许抛出一个对象
void lv_obj_set_drag_throw(lv_obj_t * obj, bool en)
说明: 就是拖拽过程中可以将对象甩出去

1.22.允许使用父类来拖拽相关操作(待理解作用)
void lv_obj_set_drag_parent(lv_obj_t * obj, bool en)
说明: 暂时不理解是干什么的?

1.23.清除保护位(待理解作用)
void lv_obj_set_protect(lv_obj_t * obj, uint8_t prot)
说明: 暂时不理解是干什么的?

1.24. 设置对象的信号函数(待理解作用)
void lv_obj_set_signal_func(lv_obj_t * obj, lv_signal_func_t fp);

1.25.为对象设置一个新的设计函数(待理解作用)
void lv_obj_set_design_func(lv_obj_t * obj, lv_design_func_t fp);

1.26.为对象分配一个新的ext数据(待理解作用)
void * lv_obj_allocate_ext_attr(lv_obj_t * obj, uint16_t ext_size);

1.27.发送一个'LV_SIGNAL_REFR_EXT_SIZE'信号给对象(待理解作用)
void lv_obj_refresh_ext_size(lv_obj_t * obj);

1.28. 为对象设置特定于应用程序的编号(待理解作用)
void lv_obj_set_free_num(lv_obj_t * obj, LV_OBJ_FREE_NUM_TYPE free_num);

1.29. 为对象设置特定于应用程序的指针(待理解作用)
void lv_obj_set_free_ptr(lv_obj_t * obj, void * free_p);

1.30. 动画的对象???(这个是纯翻译, 再理解下什么意思)
void lv_obj_animate(lv_obj_t * obj, lv_anim_builtin_t type, uint16_t time, uint16_t delay, void (*cb) (lv_obj_t *));

1.31. 返回一个指向活动屏幕的指针
lv_obj_t * lv_scr_act(void);

1.32.返回顶部图层。(在每个屏幕上都是一样的，它在正常的屏幕层之上)
lv_obj_t * lv_layer_top(void);

1.33. 返回系统层。(在每个屏幕上都是一样的，它在所有其他图层之上)
lv_obj_t * lv_layer_sys(void);

1.34. 返回对象所在的屏幕
lv_obj_t * lv_obj_get_screen(lv_obj_t * obj);

1.35.返回对象的父类对象
lv_obj_t * lv_obj_get_parent(lv_obj_t * obj);

 1.36.迭代对象的子对象(从最先创建的子对象开始到最后创建的)
lv_obj_t * lv_obj_get_child(lv_obj_t * obj, lv_obj_t * child);

 1.36.迭代对象的子对象(从最后创建的子对象开始到最开始创建的)
lv_obj_t * lv_obj_get_child_back(lv_obj_t * obj, lv_obj_t * child);

1.37.计算对象的子对象数量
uint16_t lv_obj_count_children(lv_obj_t * obj);
