Добрый день, Олег! Мы хотим запроектировать свой слой по модели **DataVault**, и у нас возникли по этому поводу вопросы. Основной вопрос заключатеся в том, что нам делать с чужими источниками, которые по своей сути являются денормализованными таблицами фактов. Т.е. как нам реализовать первый пункт:

###1. Идентификация ключевых бизнес-объектов и их атрибутов:
- Определите ключевые бизнес-объекты, с которыми работает ваша организация (например, заказы, клиенты, продукты и т. д.).
- Определите атрибуты этих бизнес-объектов, которые необходимо отслеживать во времени.###

Например, у нас есть расчет витрины, который в своей основе использует данные из таблицы смежников **s_gp_p1024_ora_svd_kb_kksb_spr_gp_ini.kksb_fsm3_emps_cbdd_lfos**:

```sql
cc360=> \d+ s_gp_p1024_ora_svd_kb_kksb_spr_gp_ini.kksb_fsm3_emps_cbdd_lfos

                                                          Table "s_gp_p1024_ora_svd_kb_kksb_spr_gp_ini.kksb_fsm3_emps_cbdd_lfos"
                Column                |            Type             | Collation | Nullable | Default | Storage  | Stats target |                       Description
--------------------------------------+-----------------------------+-----------+----------+---------+----------+--------------+-------------------------------------------------------
 report_code                          | character varying(255)      |           |          |         | extended |              | Код типа отчёта
 report_name                          | character varying(255)      |           |          |         | extended |              | Наименование типа отчёта
 report_generate_date                 | timestamp without time zone |           |          |         | plain    |              | Дата формирования отчёта
 report_begin_date                    | timestamp without time zone |           |          |         | plain    |              | Дата начала периода отчёта
 report_end_date                      | timestamp without time zone |           |          |         | plain    |              | Дата окончания периода отчёта
 report_actual_date                   | date                        |           |          |         | plain    |              | Дата актуальности отчёта
 emp_code                             | integer                     |           |          |         | plain    |              | Табельный номер
 emp_begin_date                       | date                        |           |          |         | plain    |              | Дата начала периода
 emp_end_date                         | date                        |           |          |         | plain    |              | Дата окончания периода
 div_code                             | character varying(32)       |           |          |         | extended |              | Идентификатор подразделения
 div_business_code                    | character varying(256)      |           |          |         | extended |              | Бизнес-код подразделения
 div_name                             | character varying(512)      |           |          |         | extended |              | Наименование подразделения
 div_full_name                        | character varying(300)      |           |          |         | extended |              | Полное наименование подразделения
 div_tb_name                          | character varying(512)      |           |          |         | extended |              | Наименование ТБ
 div_tb_code                          | character varying(32)       |           |          |         | extended |              | Идентификатор ТБ
 div_gosb_short_name                  | character varying(512)      |           |          |         | extended |              | Наименование ГОСБ
 div_gosb_code                        | character varying(32)       |           |          |         | extended |              | Идентификатор ГОСБ
 div_osb_name                         | character varying(512)      |           |          |         | extended |              | Наименование ОСБ
 div_osb_code                         | character varying(32)       |           |          |         | extended |              | Идентификатор ОСБ
 div_vsp_name                         | character varying(512)      |           |          |         | extended |              | Наименование ВСП
 div_vsp_code                         | character varying(32)       |           |          |         | extended |              | Идентификатор ВСП
 div_level_1_short_name               | character varying(512)      |           |          |         | extended |              | Наименование подразделения (уровень 1)
 div_level_1_code                     | character varying(32)       |           |          |         | extended |              | Идентификатор подразделения (уровень 1)
 div_level_2_short_name               | character varying(512)      |           |          |         | extended |              | Наименование подразделения (уровень 2)
 div_level_2_code                     | character varying(32)       |           |          |         | extended |              | Идентификатор подразделения (уровень 2)
 div_level_3_short_name               | character varying(512)      |           |          |         | extended |              | Наименование подразделения (уровень 3)
 div_level_3_code                     | character varying(32)       |           |          |         | extended |              | Идентификатор подразделения (уровень 3)
 div_virt_code                        | character varying(32)       |           |          |         | extended |              | Идентификатор виртуального подразделения
 div_virt_business_code               | character varying(256)      |           |          |         | extended |              | Бизнес-код виртуального подразделения
 div_virt_short_name                  | character varying(512)      |           |          |         | extended |              | Наименование виртуального подразделения
 div_type_code                        | integer                     |           |          |         | plain    |              | Идентификатор типа подразделения
 div_type_name                        | character varying(512)      |           |          |         | extended |              | Наименование типа подразделения
 emp_full_name                        | character varying(80)       |           |          |         | extended |              | ФИО сотрудника
 post_state_code                      | integer                     |           |          |         | plain    |              | Идентификатор штатной должности
 post_state_name                      | character varying(4000)     |           |          |         | extended |              | Наименование штатной должности
 post_etalon_code                     | integer                     |           |          |         | plain    |              | Идентификатор эталонной должности
 post_etalon_name                     | character varying(512)      |           |          |         | extended |              | Наименование эталонной должности
 func_role_code_m                     | integer                     |           |          |         | plain    |              | Идентификатор функциональной роли (Месяц)
 func_role_name_m                     | character varying(512)      |           |          |         | extended |              | Наименование функциональной роли (Месяц)
 func_role_code_q                     | integer                     |           |          |         | plain    |              | Идентификатор функциональной роли (квартал)
 func_role_name_q                     | character varying(512)      |           |          |         | extended |              | Наименование функциональной роли (квартал)
 func_role_code_y                     | integer                     |           |          |         | plain    |              | Идентификатор функциональной роли (год)
 func_role_name_y                     | character varying(512)      |           |          |         | extended |              | Наименование функциональной роли (год)
 emp_is_learner_q                     | character varying(1)        |           |          |         | extended |              | Признак ученика (квартал)
 emp_is_calc_by_norm_q                | character varying(1)        |           |          |         | extended |              | Признак расчета премии по нормативу (квартал)
 emp_is_ex_ind_component_q            | character varying(1)        |           |          |         | extended |              | Признак исключания индивидуальной составляющей (квартал)
 grade_code                           | character varying(8)        |           |          |         | extended |              | Идентификатор разряда сотрудника
 emp_is_include_in_premium_list_m     | character varying(1)        |           |          |         | extended |              | Признак участия в премировании (месяц)
 emp_is_include_in_premium_list_q     | character varying(1)        |           |          |         | extended |              | Признак участия в премировании (квартал)
 emp_post_state_begin_date            | date                        |           |          |         | plain    |              | Дата вступления в должность
 emp_is_calc_by_norm_m                | character varying(1)        |           |          |         | extended |              | Признак расчета премии по нормативу (месяц)
 div_urm_code                         | character varying(32)       |           |          |         | extended |              | Идентификатор УРМ
 div_urm_short_name                   | character varying(512)      |           |          |         | extended |              | Наименование УРМ
 vert_type_code                       | integer                     |           |          |         | plain    |              | Идентификатор вертикали
 vert_type_name                       | character varying(250)      |           |          |         | extended |              | Наименование вертикали
 emp_is_in_maternity_leave_begin_date | date                        |           |          |         | plain    |              | Дата начала декретного отпуска
 emp_is_in_maternity_leave_end_date   | date                        |           |          |         | plain    |              | Дата окончания декретного отпуска
 emp_is_in_maternity_leave            | character varying(1)        |           |          |         | extended |              | Признак декретного отпуска
 object_locality_name                 | character varying(1024)     |           |          |         | extended |              | Населенный пункт
 emp_email                            | character varying(255)      |           |          |         | extended |              | E-mail сотрудника
 emp_fire_date                        | date                        |           |          |         | plain    |              | Дата увольнения
 emp_is_learner_m                     | character varying(1)        |           |          |         | extended |              | Признак ученика (месяц)
 emp_is_ex_ind_component_m            | character varying(1)        |           |          |         | extended |              | Исключение индивидуальной составляющей (месяц)
 halfperiod_comment                   | character varying(512)      |           |          |         | extended |              | Комментарий к периоду
 div_real_vsp_code                    | character varying(255)      |           |          |         | extended |              | Фактическое подразделение
 post_state_begin_date                | date                        |           |          |         | plain    |              | Дата начала полупериода штатной должности
 post_state_end_date                  | date                        |           |          |         | plain    |              | Дата окончания полупериода штатной должности
 emp_head_code                        | integer                     |           |          |         | plain    |              | Табельный номер руководителя
 div_kpk_short_name                   | character varying(512)      |           |          |         | extended |              | Краткое наименование КПК
Options: appendonly=true, orientation=row
```

Собственно данная таблица представлена в виде денормализованного отношения *(таблица фактов)* с транзакционной информацией. 
- Как нам ее привести к **3НФ**, чтобы затем на основе конкретных бизнес-сущностей построить **hub**, **sat**, **link**? 
- Запрашивать информацию у смежников о данном источнике и идти в самое *начало начал*, или возможно использовать какой-то *гибридный метод*, суть которого в том, чтобы такие таблицы фактов оставлять в том же денормализованном виде, а переводить в **DV** уже только свои собственные сущности, и далее их каким-то образом соединять между собой при расчете?
