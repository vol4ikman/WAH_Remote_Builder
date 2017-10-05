<template>

  <div class="builder" :class="{rtl : is_rtl}">

        <div class="locale-selector">
            <button type="button" class="lang-en" @click="updateLocale('en')" :class="{'activeLang' : this.current_lang == 'en'}">
                <img src="static/images/us.svg" alt="English">
            </button>
            <button type="button" class="lang-ru" @click="updateLocale('ru')" :class="{'activeLang' : this.current_lang == 'ru'}">
                <img src="static/images/ru.svg" alt="Russian">
            </button>
            <button type="button" class="lang-he" @click="updateLocale('he')" :class="{'activeLang' : this.current_lang == 'he'}">
                <img src="static/images/il.svg" alt="Hebrew">
            </button>
        </div>

        <div class="page-titles">
            <h1 class="page-title">{{ locale[current_lang].title }}</h1>
            <h2 class="page-sub-title"><span>{{ locale[current_lang].subtitle }}</span></h2>
        </div>

        <hr>

        <div class="step-header">
            <span>{{ locale[current_lang].step1_title }}</span>
        </div>

        <div class="builder-form-wrapper">

            <form class="builder-form" method="post" @submit.prevent="generateCode()">

                <div class="form-row">
                    <h3><span>{{ locale[current_lang].user_settings }}:</span></h3>
                    <label>
                        <span>{{ locale[current_lang].user_id }}:</span>
                        <input type="text" v-model="json.user.id">
                    </label>
                    <label>
                        <span>{{ locale[current_lang].token }}:</span>
                        <input type="text" v-model="json.user.token">
                    </label>
                </div>

                <div class="form-row">
                    <h3><span>{{ locale[current_lang].theme_settings }}:</span></h3>
                    <label>
                        <span>{{ locale[current_lang].theme_name }}:</span>
                        <select v-model="json.theme.name">
                            <option value="dark">{{ locale[current_lang].dark_label }}</option>
                            <option value="light">{{ locale[current_lang].light_label }}</option>
                        </select>
                    </label>
                    <label>
                        <span>{{ locale[current_lang].layout }}:</span>
                        <select v-model="json.theme.type">
                            <option value="1">{{ locale[current_lang].standart_label }}</option>
                            <option value="2">{{ locale[current_lang].wide_label }}</option>
                            <option value="3">{{ locale[current_lang].magic_label }}</option>
                        </select>
                    </label>
                    <label>
                        <span>{{ locale[current_lang].sidebar_position }}:</span>
                        <select v-model="json.theme.position">
                            <option value="right">{{ locale[current_lang].right_label }}</option>
                            <option value="left">{{ locale[current_lang].left_label }}</option>
                        </select>
                    </label>
                </div>

                <div class="form-row">
                    <h3><span>{{ locale[current_lang].setup_settings }}:</span></h3>

                    <label>
                        <span>{{ locale[current_lang].language }}:</span>
                        <select v-model="json.setup.lang">
                            <option value="en">{{ locale[current_lang].english_label }}</option>
                            <option value="ru">{{ locale[current_lang].russian_label }}</option>
                            <option value="he">{{ locale[current_lang].hebrew_label }}</option>
                        </select>
                    </label>

                    <label class="inline">
                        <input type="checkbox" v-model="json.setup.debug">
                        <span>{{ locale[current_lang].enable_debug }}</span>
                    </label>

                    <label class="inline">
                        <input type="checkbox" v-model="json.setup.stats">
                        <span>{{ locale[current_lang].enable_statistics }}</span>
                    </label>

                    <label class="inline">
                        <input type="checkbox" v-model="json.setup.hide.remove_animations">
                        <span>{{ locale[current_lang].disable_remove_animations }}</span>
                    </label>

                    <label class="inline">
                        <input type="checkbox" v-model="json.setup.hide.highlight_titles">
                        <span>{{ locale[current_lang].disable_highlight_titles }}</span>
                    </label>

                    <label class="inline">
                        <input type="checkbox" v-model="json.setup.hide.highlight_links">
                        <span>{{ locale[current_lang].disable_highlight_links }}</span>
                    </label>

                    <label class="inline">
                        <input type="checkbox" v-model="json.setup.hide.keyboard_navigation">
                        <span>{{ locale[current_lang].disable_keyboard_navigations }}</span>
                    </label>

                    <label class="inline">
                        <input type="checkbox" v-model="json.setup.hide.invert_colors">
                        <span>{{ locale[current_lang].disable_invert_colors }}</span>
                    </label>

                    <label class="inline">
                        <input type="checkbox" v-model="json.setup.hide.readable_fonts">
                        <span>{{ locale[current_lang].disable_readable_fonts }}</span>
                    </label>
                </div>

                <button type="submit" class="submit">{{ locale[current_lang].generate_code }}</button>

            </form>

            <div class="builder-live">
                <h3><span>{{ locale[current_lang].live_preview_title }}:</span></h3>

                <div class="live-preview-bg-colors" v-if="code">
                    <button class="bg-button black" @click="switchLivePreviewBG('#000')"></button>
                    <button class="bg-button white" @click="switchLivePreviewBG('#FFF')"></button>
                    <button class="bg-button grey" @click="switchLivePreviewBG('#ddd')"></button>
                </div>

                <div class="builder-live-content" v-if="code" :style="{'background' : defaultLivePreviewBG }">
                    <div id="wp_access_helper_container" class="accessability_container active"
                        :class="{
                            'dark_theme' : json.theme.name == 'dark',
                            'light_theme' : json.theme.name == 'light',
                            'accessibility-location-right' : json.theme.position == 'right',
                            'accessibility-location-left' : json.theme.position == 'left'
                            }"
                        >
                        <div id="access_container" aria-hidden="false">
                            <button type="button" class="close_container wahout">Close</button>
                            <div class="access_container_inner">

                                <div class="a_module wah_font_resize">
                                    <div class="a_module_title">Font Resize</div>
                                    <div class="a_module_exe font_resizer">
                                        <button type="button" class="wah-action-button smaller wahout">A-</button>
                                        <button type="button" class="wah-action-button larger wahout">A+</button>
                                        <button type="button" class="wah-action-button wah-font-reset wahout">Reset font size</button>
                                    </div>
                                </div>

                                <div class="a_module wah_keyboard_navigation" v-if="!json.setup.hide.keyboard_navigation">
                                    <div class="a_module_exe">
                                        <button type="button" class="wah-action-button wahout wah-call-keyboard-navigation">Keyboard navigation</button>
                                    </div>
                                </div>

                                <div class="a_module wah_readable_fonts" v-if="!json.setup.hide.readable_fonts">
                                    <div class="a_module_exe readable_fonts">
                                        <button type="button" class="wah-action-button wahout wah-call-readable-fonts">Readable Font</button>
                                    </div>
                                </div>

                                <div class="a_module wah_contrast_trigger">
                                    <div class="a_module_title">Contrast</div>
                                    <div class="a_module_exe">
                                        <button type="button" id="contrast_trigger" class="contrast_trigger wah-action-button wahout wah-call-contrast-trigger">Choose color</button>
                                    </div>
                                </div>

                                <div class="a_module wah_highlight_links" v-if="!json.setup.hide.highlight_links">
                                    <div class="a_module_exe">
                                        <button type="button" class="wah-action-button wahout wah-call-highlight-links">Highlight Links</button>
                                    </div>
                                </div>

                                <div class="a_module wah_highlight_titles" v-if="!json.setup.hide.highlight_titles">
                                    <div class="a_module_exe">
                                        <button type="button" class="wah-action-button wahout wah-call-highlight-titles">Highlight Titles</button>
                                    </div>
                                </div>

                                <div class="a_module wah_clear_cookies">
                                    <div class="a_module_exe">
                                        <button type="button" class="wah-action-button wahout wah-call-clear-cookies">Clear cookies</button>
                                    </div>
                                </div>

                                <div class="a_module wah_greyscale">
                                    <div class="a_module_exe">
                                        <button type="button" id="greyscale" class="greyscale wah-action-button wahout wah-call-greyscale">Images Greyscale</button>
                                    </div>
                                </div>

                                <div class="a_module wah_invert" v-if="!json.setup.hide.invert_colors">
                                    <div class="a_module_exe">
                                        <button type="button" class="wah-action-button wahout wah-call-invert">Invert Colors</button>
                                    </div>
                                </div>

                                <div class="a_module wah_remove_animations" v-if="!json.setup.hide.remove_animations">
                                    <div class="a_module_exe">
                                        <button type="button" accesskey="a" class="wah-action-button wahout wah-call-remove-animations">Remove Animations</button>
                                    </div>
                                </div>


                                </div>
                            </div>
                        </div>
                </div>
            </div>

        </div>

        <div class="buider-preview builder-code" v-if="code">

            <div class="step-header">
                <span>{{ locale[current_lang].step2_title }}</span>
            </div>

            <h3><span>{{ locale[current_lang].code_title }}:</span></h3>
            <textarea v-model="code" @click="selectCode()" ref="codeEditor"></textarea>
            <div class="notice-wrapper" v-if="code">
                <div class="notice" v-if="!json.user.id">
                    {{ locale[current_lang].error_id_label }}
                </div>
                <div class="copied" v-if="copied">
                    {{ locale[current_lang].notice_copied }}
                </div>
            </div>

            <div class="step-header">
                <span>{{ locale[current_lang].step3_title }}</span>
            </div>

            <div class="step-description step3-description" v-html="locale[current_lang].step3_description"></div>

        </div>

    </div>

</template>

<script>

export default {
    name: 'hello',
    data () {
        return {
            code                 : '',
            copied               : false,
            current_lang         : 'en',
            is_rtl               : false,
            defaultLivePreviewBG : 'black',
            locale : {
                en : {
                    step1_title                  : 'Step 1 - Generate script code',
                    step2_title                  : 'Step 2 - Copy code',
                    step3_title                  : 'Step 3 - Insert code template into your page',
                    step3_description            : '<p>Paste the code just right after closing body tag.</p>',
                    title                        : 'Welcome to WAH PRO Remote script Builder',
                    subtitle                     : 'WP Accessibility Helper by WAH TEAM',
                    user_settings                : 'User settings',
                    user_id                      : 'User ID',
                    token                        : 'Token',
                    theme_settings               : 'Theme Settings',
                    theme_name                   : 'Theme color',
                    layout                       : 'Layout',
                    sidebar_position             : 'Sidebar position',
                    setup_settings               : 'Setup Settings',
                    language                     : 'Language',
                    enable_debug                 : 'Enable debug?',
                    enable_statistics            : 'Enable statistics?',
                    disable_remove_animations    : 'Disable "Remove animations" button?',
                    disable_highlight_titles     : 'Disable "Highlight titles" button?',
                    disable_highlight_links      : 'Disable "Highlight links" button?',
                    disable_keyboard_navigations : 'Disable "Keyboard navigation" button?',
                    disable_invert_colors        : 'Disable "Invert colors" button?',
                    disable_readable_fonts       : 'Disable "Readable fonts" button?',
                    code_title                   : 'Code',
                    generate_code                : 'Generate code & Preview',
                    english_label                : 'English',
                    russian_label                : 'Russian',
                    hebrew_label                 : 'Hebrew',
                    right_label                  : 'Right',
                    left_label                   : 'Left',
                    standart_label               : 'Standart (1 columns)',
                    wide_label                   : 'Wide (2 columns)',
                    magic_label                  : 'Magic (animation effect)',
                    dark_label                   : 'Dark',
                    light_label                  : 'Light',
                    error_id_label               : 'Important notice: User ID is required.',
                    notice_copied                : 'Code has been copied!',
                    live_preview_title           : 'Live preview'
                },
                ru : {
                    step1_title                  : 'Шаг 1 - Заполнить форму и получить код.',
                    step2_title                  : 'Шаг 2 - Скопировать код',
                    step3_title                  : 'Шаг 3 - Вставить код на вашу страничку',
                    step3_description            : '<p>Вставить данный код сразу после закрытия тэга body</p>',
                    title                        : 'Добро пожаловать в WAH PRO Remote script Builder',
                    subtitle                     : 'WP Accessibility Helper by WAH TEAM',
                    user_settings                : 'Настройки пользователя',
                    user_id                      : 'ID пользователя',
                    token                        : 'Токен (секретный ключ)',
                    theme_settings               : 'Настройки шаблона',
                    theme_name                   : 'Цвет шаблона',
                    layout                       : 'Структура шаблона',
                    sidebar_position             : 'Позиция',
                    setup_settings               : 'Настройки',
                    language                     : 'Язык',
                    enable_debug                 : 'Включить debug?',
                    enable_statistics            : 'Включить статистику?',
                    disable_remove_animations    : 'Отключить кнопку "Отключение анимации"?',
                    disable_highlight_titles     : 'Отключить кнопку "Подсветка заголоков"?',
                    disable_highlight_links      : 'Отключить кнопку "Подсветка ссылок"?',
                    disable_keyboard_navigations : 'Отключить кнопку "Навигации с клавиатуры"?',
                    disable_invert_colors        : 'Отключить кнопку "Искажение цвета"?',
                    disable_readable_fonts       : 'Отключить кнопку "Читаемый шрифт"?',
                    code_title                   : 'Код',
                    generate_code                : 'Генерировать код',
                    english_label                : 'Английский (США и Великобритания)',
                    russian_label                : 'Русский (Россия)',
                    hebrew_label                 : 'Иврит (Израиль)',
                    right_label                  : 'Справа',
                    left_label                   : 'Слева',
                    standart_label               : 'Стандартный (1 колонка)',
                    wide_label                   : 'Широкий (2 колонки)',
                    magic_label                  : 'Волшебный (с эффектом анимации)',
                    dark_label                   : 'Тёмный',
                    light_label                  : 'Светлый',
                    error_id_label               : 'Внимание! "User ID" это обязательный параметр. Без него ваш скрипт работать НЕ будет.',
                    notice_copied                : 'Код скопирован в буфер обмена!',
                    live_preview_title           : 'Предпросмотр'
                },
                he : {
                    step1_title                  : 'שלב 1 - למלא טופס ולקבל קוד התמאה.',
                    step2_title                  : 'שלב 2 - להעתיק קוד',
                    step3_title                  : 'שלב 3 - להדביר קוד לעמוד',
                    step3_description            : '<p>תדביקו קוד שקבלתם ישר אחרי סגירה של תגית body.</p>',
                    title                        : 'ברוכים הבאים ל WAH PRO Remote script Builder',
                    subtitle                     : 'WP Accessibility Helper by WAH TEAM',
                    user_settings                : 'הגדרות משתמש',
                    user_id                      : 'מזהה של משתמש',
                    token                        : 'מפתח',
                    theme_settings               : 'הגדרות תבנית',
                    theme_name                   : 'צבע של תבנית',
                    layout                       : 'פריסת תבנית',
                    sidebar_position             : 'מיקום של סרגל צד',
                    setup_settings               : 'הדגרות',
                    language                     : 'שפה',
                    enable_debug                 : 'הפעל איתור שגיות?',
                    enable_statistics            : 'הפעל סטטיסטיקה?',
                    disable_remove_animations    : 'הסר כפתור "הסר אנימציות"?',
                    disable_highlight_titles     : 'הסר כפתור "סימון כותרות"?',
                    disable_highlight_links      : 'הסר כפתור "סימון קישורים"?',
                    disable_keyboard_navigations : 'הסר כפתור "ניווט מקלדת"?',
                    disable_invert_colors        : 'הסר כפתור "היפוך צבעים"?',
                    disable_readable_fonts       : 'הסר כפתור "פונט קריא"?',
                    code_title                   : 'קוד',
                    generate_code                : 'הצג קוד',
                    english_label                : 'אנגלית',
                    russian_label                : 'רוסית',
                    hebrew_label                 : 'עברית',
                    right_label                  : 'צד ימין',
                    left_label                   : 'צד שמאל',
                    standart_label               : 'רגיל (עמודה 1)',
                    wide_label                   : 'רחב (2 עמודות)',
                    magic_label                  : 'מגיק (אפקט אנימציה)',
                    dark_label                   : 'כהה',
                    light_label                  : 'בהיר',
                    error_id_label               : 'שימו לב! מזהה של משתמש הוא שדה חובה. ללא מזהה תקין קוד לא יעבוד.',
                    notice_copied                : 'קוד הועתק בהצלחה!',
                    live_preview_title           : 'תצוגה מקדימה'
                }
            },

            json: {
                user : {
                    id: '',                // user ID
                    token : ''           // user secret token
                },
                theme: {
                    name     : 'dark',           // dark || light
                    position : 'right',          // right || left
                    type     : 1,                // 1 = standart, 2 = wide, 3 = magic
                    //icon     : 'style/banner.jpg'               // url to image 48x48px
                },
                setup: {
                    debug    : true,               // true or false
                    stats    : true,
                    lang     : 'en',               // en, ru, he [default en]
                    hide : {
                        remove_animations   : false,
                        highlight_titles    : false,
                        highlight_links     : false,
                        keyboard_navigation : false,
                        invert_colors       : false,
                        readable_fonts      : false
                    }
                }
            },
            user: {
                id    : '',
                token : ''
            }
        }
    },
    methods: {
        generateCode(){
            var str_start = 'var wahproConfig = ';
            var str = JSON.stringify(this.json, undefined, 4);
            var str_end = ';';
            var str_result = str_start + str + str_end;
            this.code = str_result;
        },
        selectCode(){
            var textarea = this.$refs.codeEditor;
            textarea.select();
            document.execCommand("copy");
            this.copied = true;
            setTimeout( function(){
                this.copied = false;
            }.bind(this), 3000);
        },
        updateLocale(lang){
            this.is_rtl = ( lang == 'he' ) ? true : false;
            this.current_lang = lang;
        },
        switchLivePreviewBG(color){
            this.defaultLivePreviewBG = color;
        }
    }
}
</script>
