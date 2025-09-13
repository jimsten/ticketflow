# DB 设计（草案）
表：user, schedule(场次), inventory(库存), order(订单)
要点：唯一约束避免重复下单；库存扣减原子性；必要索引。
