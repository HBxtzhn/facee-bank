多版本并发控制，通过每行数据维护多个版本实现读写不冲突。InnoDB: 每行有trx_id(最后修改事务ID)和roll_pointer(指向undo log旧版本)。ReadView记录当前活跃事务列表。查询时: 数据trx_id<ReadView最小活跃事务ID→已提交可见; 在活跃列表中→不可见沿roll_pointer找更早版本。
