# Development assignment back-end developer

Create a .NET Core (version 2.1 or higher) RESTful API with the following exposed actions:
* Create and persist a new product with the following mandatory fields:
    * Id (Guid)
    * Name (string)
    * Description (string)
* Update an existing product
* Delete an existing product
* Get an existing product by its identifier
* Get a paged list of products (query parameters are page and page size) where the response contains the following information:
    * the total count of persisted products
    * the products on the requested page

Solution:

--1) Please run the below scripts in SQL server

CREATE TABLE [dbo].[Product] (
    [Id]           GUID NOT NULL,
    [productName]        VARCHAR (80) NOT NULL,
    [Description]        VARCHAR (100) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY CLUSTERED ([Id])
);

Insert into dbo.Product values ('950bc070-8faa-4e91-bf80-c325b7197a13', 'ABCShampoo', 'Shampoo for smooth hair');
Insert into dbo.Product values ('f08043ae-1488-41b1-a4f1-62bae83e9f66', 'DEFConditioner', 'Conditioner for smooth hair');
//API
public class ProductClass
{
//2) Updating an existing product
public void UpdateProduct(Product prod, GUID productId)
{
   using (MyDataContext db = new MyDataContext())
   {
   Product dbProduct = (from db.Products p
                  where p.Id equals productId
                  select p).First();   
    dbProduct.productName = prod.productName;
    dbProduct.Description = prod.Description;
        db.SaveChanges();
   }  
}

//3) Delete an existing product
public void DeleteProduct(GUID productId)
{
   using (MyDataContext db = new MyDataContext())
   {
   Product dbProduct = (from db.Products p
                  where p.Id equals productId
                  select p).First(); 
    db.Product.Remove(dbProduct);
    db.SaveChanges();
   }  
}

//4) Get product by Id
public Product GetProductById(GUID productId)
{
using (MyDataContext db = new MyDataContext())
   {
   Product dbProduct = (from db.Products p
                  where p.Id equals productId
                  select p).First();   
   return dbProduct;
   }  
}

//5) Pagination
public void PagedProduct(int page, int size, out int total, out List<Products> pList)
 {
   using (MyDataContext db = new MyDataContext())
   {
   var dbProdList = (from db.Products p
                  where p.Active == true
                  select p).ToList();   
   
   total = dbProdList.ToList().Count();
   //pagination logic
   var resultPage = dbProdList.Skip(size * page).Take(size);
   pList = resultPage.ToList();
   }  
 }
}//Class closure
