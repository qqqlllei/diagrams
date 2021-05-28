### 修改字段
    alter table mod_relative_time change dateStart dateStart varchar(30) DEFAULT NULL COMMENT '日期区间-开始日期';
    alter table mod_relative_time change dateEnd dateEnd varchar(30) DEFAULT NULL COMMENT '日期区间-结束日期';



    