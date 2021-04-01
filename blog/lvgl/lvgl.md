# LVGL


## style

```c
    static lv_style_t style;
    lv_style_init(&style);
    lv_style_set_radius(&style, LV_STATE_DEFAULT, 5);				//
    lv_style_set_bg_opa(&style, LV_STATE_DEFAULT, LV_OPA_COVER);
    lv_style_set_bg_color(&style, LV_STATE_DEFAULT, LV_COLOR_SILVER);
    lv_style_set_border_width(&style, LV_STATE_DEFAULT, 2);
    lv_style_set_border_color(&style, LV_STATE_DEFAULT, LV_COLOR_BLUE);

    lv_style_set_pad_top(&style, LV_STATE_DEFAULT, 10);
    lv_style_set_pad_bottom(&style, LV_STATE_DEFAULT, 10);
    lv_style_set_pad_left(&style, LV_STATE_DEFAULT, 10);
    lv_style_set_pad_right(&style, LV_STATE_DEFAULT, 10);

    lv_style_set_text_letter_space(&style, LV_STATE_DEFAULT, 5);
    lv_style_set_text_line_space(&style, LV_STATE_DEFAULT, 20);
    lv_style_set_text_decor(&style, LV_STATE_DEFAULT, LV_TEXT_DECOR_UNDERLINE);
    lv_style_set_text_color(&style, LV_STATE_DEFAULT, LV_COLOR_BLUE);
    lv_style_set_text_font(&style, LV_STATE_DEFAULT, &lv_font_montserrat_18);
    
    cmsg = lv_label_create(content, NULL);
    lv_obj_add_style(cmsg, LV_LABEL_PART_MAIN, &style);
```

## Theme

```c
typedef struct _lv_theme_t {
    lv_theme_apply_cb_t apply_cb;
    lv_theme_apply_xcb_t apply_xcb; /*Deprecated: use `apply_cb` instead*/
    struct _lv_theme_t * base;    /**< Apply the current theme's style on top of this theme.*/
    lv_color_t color_primary;
    lv_color_t color_secondary;
    const lv_font_t * font_small;
    const lv_font_t * font_normal;
    const lv_font_t * font_subtitle;
    const lv_font_t * font_title;
    uint32_t flags;
    void * user_data;
} lv_theme_t;
```

```
theme.color_primary
theme.color_secondary
theme.font_small
theme.font_normal
theme.font_subtitle
theme.font_title
theme.flags
```