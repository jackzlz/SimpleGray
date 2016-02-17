---
layout: post
title: hibernate查询SQL语句返回自定义实体对象
category: tech
---

修改了一下，增加类型转换。用到了apache的beanutils工具包。

<!--more-->

~~~ java
  public static <T> List<T> getEntityObjectListByFullSql(
          final String fullSql, final Class<T> clzz) throws Exception {
      return getHibernateTemplate().execute(
              new HibernateCallback<List<T>>() {
                  @Override
                  public List<T> doInHibernate(Session session)
                          throws HibernateException, SQLException {

                      SQLQuery sqlQuery = session.createSQLQuery(fullSql);

                      sqlQuery.setResultTransformer(CriteriaSpecification.ALIAS_TO_ENTITY_MAP);

                      @SuppressWarnings("unchecked")
                      List<Map<String, Object>> list = (List<Map<String, Object>>) sqlQuery
                              .list();

                      if (CollectionUtils.isEmpty(list)) {
                          return null;
                      }

                      List<T> result = new ArrayList<T>();
                      try          .getBeanInfo(clzz).getPropertyDescriptors();
                          for (Map<String, Object> map : list) {
                              T t = clzz.newInstance();
                              for (Entry<String, Object> entry : map
                                      .entrySet()) {
                                  String attrName = entry.getKey()
                                          .toLowerCase();
                                  for (PropertyDescriptor prop : props) {
                                      if (!attrName.equals(prop.getName())) {
                                          continue;
                                      }
                                      Method method = prop.getWriteMethod();

                                      Object value = entry.getValue();
                                      if (value != null) {
                                          value = ConvertUtils.convert(value,
                                                  prop.getPropertyType());
                                      }
                                      method.invoke(t, value);
                                  }
                              }
                              result.add(t);
                          }
                      } catch (Exception e) {
                          throw new RuntimeException(e);
                      }
                      return result;
                  }
              });
  }
~~~

~~~ java
  @Override
  public List<PsndocInfo> getAllPsndocList() {

      StringBuilder sql = new StringBuilder();
      sql.append("\n");
      sql.append("SELECT doc.pk_org, org.name orgname, org.code orgcode, doc.name psnname, doc.code psncode,doc.enablestate\n");
      sql.append("  FROM bd_psndoc doc\n");
      sql.append(" inner join org_orgs org\n");
      sql.append("    on doc.pk_org = org.pk_org");

      return super.queryBySql(sql.toString(), PsndocInfo.class);
  }
~~~
