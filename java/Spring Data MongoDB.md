##### $or

Criteria criteria = new Criteria().orOperator(Criteria.where("id").is(id_a), Criteria.where("id").is(id_b));

