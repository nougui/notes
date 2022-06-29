# Dotnet

## Rider Scaffold

```sh
dotnet ef dbcontext scaffold "data source=mssql.domain.com;initial catalog=dev;User ID=XAPPLICATION;Password=password;Trusted_Connection=False;MultipleActiveResultSets=true" Microsoft.EntityFrameworkCore.SqlServer -o Models --no-pluralize -c "AutoTempContext" -d -p Persistence
```
