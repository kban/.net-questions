# .NET questions
This repo contains most interesting (imho) questions/tasks which i've been asked on interviews

## 1. What's wrong with the code and how to fix it.
```
using System.Collections.Generic;
using System.Linq;
namespace Basic.Tests.Quiz
{
    public static class Enumerable
    {    
        public class UserWithDep
        {
            public User User { get; set; }
            public Department Department { get; set; }
        }        
        public class User
        {
            public string Name { get; set; }
            public int? Department { get; set; }
        }
        public class Department
        {
            public int? DepartmentID { get; set; }
            public string Namе { get; set; }
        }
        public static IEnumerable<UserWithDep> Run(IEnumerable<User> users, IEnumerable<Department> departments)
        {
            if (users.Count() == 0)
                return new List<UserWithDep>();
            var result = new List<UserWithDep>();            
            foreach (var user in users)
            {
                result.Add(new UserWithDep()
                {
                    User = user,
                    Department = departments.Single(d => d.DepartmentID == user.Department)
                });
            }
            return result;
        }
    }
}
```
### What's wrong:
```
using System.Collections.Generic;
using System.Linq;
namespace Basic.Tests.Quiz
{
    public static class Enumerable
    {    
        public class UserWithDep
        {
            public User User { get; set; }
            public Department Department { get; set; }
        }        
        public class User
        {
            public string Name { get; set; }
            public int? Department { get; set; }
        }
        public class Department
        {
            public int? DepartmentID { get; set; }
            public string Namе { get; set; }
        }
        public static IEnumerable<UserWithDep> Run(IEnumerable<User> users, IEnumerable<Department> departments)
        {
            if (users.Count() == 0) ///**Count() works too slow as it should get thrue all the collection. We need to use Any() instead.**

                return new List<UserWithDep>();
            var result = new List<UserWithDep>();            
            foreach (var user in users)
            ///**foreach() works too slow. Especialy when some constructions like .Take are used after this code. So we need to refactor this part of code**
            {
                result.Add(new UserWithDep()
                {
                    User = user,
                    Department = departments.Single(d => d.DepartmentID == user.Department) ////** Single() is really bad here. Single checks that there is only 1 searched item in the collection, we don't need it here. Aso departments collection can be large, and it's better to create some Dictionary like {key: Id, value: Departmet instance} and get the department by key from this collection, it'll be faster **
                });
            }
            return result;
        }
    }
}
```

### Fix
```
using System.Collections.Generic;
using System.Linq;

namespace Basic.Tests.Quiz
{
    public static class Enumerable
    {
        public class UserWithDep
        {
            public User User { get; set; }
            public Department Department { get; set; }
        }

        public class User
        {
            public string Name { get; set; }
            public int? Department { get; set; }
        }

        public class Department
        {
            public int? DepartmentID { get; set; }
            public string Namе { get; set; }
        }

        public static IEnumerable<UserWithDep> Run(IEnumerable<User> users, IEnumerable<Department> departments)
        {       
            // var result = new List<UserWithDep>(); -- Solution #1 - using Select
            
            var disct = department.Where(DepartmentID != null).Select(dp => new Discionary {key=DepartmentID, value = dp});

           //    return users.Where(x => departments.Any(d => d.Department != null && d.Department == x.Department))
           //     .Select(x=> new UserWithDep{ User = x, disct(d.DepartmentID)} );  -- Solution #1 - using Select
            
            ////  -- Solution #2 - using yield return. This solution works faster with Take(N) constructions and huge lists.
            foreach (var user in users)
            {
                yield return UserWithDep()
                {
                    User = user,
                    Department = disct(user.Department)
                });
            }
        }
    }
}
```

## 2. What's the result and why?
```
using System;
using System.Collections.Generic;
using System.Linq;

namespace Basic.Tests.Quiz
{
    public class UnitTest6
    {
        public void Test1()
        {
            foreach (var item in Get(2).ToArray())
            {
                Console.WriteLine(item); // **Result: 01 01 because .ToArray() will execute Get method and this will give 01 and then foreach will iterate thrue (0,1) and also give 01 **
            }
        }

        public void Test11()
        {
            foreach (var item in Get(2))
            {
                Console.WriteLine(item); // **Result: 00 01 because foreach() will iterate thrue Get result and since it returns yield it will be iterated on each foreach() step (0,1) and also give 01 **
            }
        }

        private static IEnumerable<int> Get(int count)
        {
            for (int index = 0; index < count; index++)
            {
                Console.WriteLine(index);
                yield return index;
            }
        }
    }
}
```

## 3. What's wrong with the code and how to fix it.
```
using System;
using System.Collections.Generic;
using System.Data.Entity;

namespace TestEf
{

    public class ArticleEntity
    {
        public int Id { get; set; }
        public string Header { get; set; }
        public DateTime Published { get; set; }

        public virtual ICollection<TagEntity> Tags { get; set; }
    }

    public class TagEntity
    {
        public int Id { get; set; }
        public string Tag { get; set; }
        public virtual ICollection<ArticleEntity> Articles { get; set; }
    }

    public class MediaNewsContext : DbContext
    {
        public DbSet<ArticleEntity> Articles { get; set; }
        public DbSet<TagEntity> Tags { get; set; }

        public MediaNewsContext(string conn) : base(conn)
        {

        }
    }

}

using System.Collections.Generic;
using System.Linq;

namespace TestEf
{
    public interface INewsGetter
    {
        IEnumerable<ArticleEntity> GetTop10(string tag, int month, int year);
        IQuerable<ArticleEntity> GetByTag(string tag);
    }

    public class NewsGetter : INewsGetter
    {
        private static MediaNewsContext _dbContext;
        public NewsGetter(string conn)
        {
            _dbContext = new MediaNewsContext(conn);
        }

        public IEnumerable<ArticleEntity> GetTop10(string tag, int month, int year)
        {
            return GetByTag(tag)
              .Where(x => x.Published.Month == month && x.Published.Year == year)
              .Take(10);
        }

        public IQuerable<ArticleEntity> GetByTag(string tagName)
        {
            return _dbContext.Tags.FirstOrDefault(t => t.Tag == tagName).Articles;
        }
    }
}
```
