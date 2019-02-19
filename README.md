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
