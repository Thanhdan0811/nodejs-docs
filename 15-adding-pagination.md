# pagination links

```
/// shop.js controller
const ITEMS_PER_PAGE = 2;
exports.getIndex = (req, res, next) => {
  const page = req.query.page;

  Product.find()
    .skip((page -1 ) * ITEMS_PER_PAGE)
    .LIMIT(ITEMS_PER_PAGE)
    .then()


}


```
- With sql : https://stackoverflow.com/questions/3799193/mysql-data-best-way-to-implement-paging
- sql with Sequelize : https://sequelize.org/master/manual/model-querying-basics.html;

# Preparing Pagination Data

```
/// shop.js controller
const ITEMS_PER_PAGE = 2;
exports.getIndex = (req, res, next) => {
  const page = +req.query.page || 1;
  let totalItems;
  Product.find().countDocuments()
  .then(numProducts => {
    totalItems = numProducts;
    return Product.find()
    .skip((page -1 ) * ITEMS_PER_PAGE)
    .LIMIT(ITEMS_PER_PAGE)
  })
  .then(products => {
    res.render('shop/index', {
      prods: products,
      pageTitle: 'Shop',
      path: '/',
      totalProducts: totalItems,
      hasNextPage: ITEMS_PER_PAGE * page < totalItems,
      hasPreviousPage: page > 1,
      nextPage: page + 1,
      previousPage: page -1,
      lastPage: Math.ceil(totalItems / ITEMS_PER_PAGE)
    })
  });

/// Note <a href="?page=1">1</a> !== <a href="/?page=1">1</a> => relative path and absolute path.


}
```
