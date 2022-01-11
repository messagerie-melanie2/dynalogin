# Version recente de debian

Pour utiliser dynalogin sur des version recente de debian (>8) 
il faut corriger le code pour la lecture de la base de donnees

Il faut egalement ajouter des options de compilation dans le debian/rules

## option de compilation (pour la recompilation du paquet)

dans : `dynalogin-1.0.0/debian/rules`

modifier la ligne 6 comme suit :

    CFLAGS = `apr-1-config --cflags --cppflags --includes` -I/usr/include/liboath -fPIC -DPIC

## correction de lecture de BDD

dans le fichier : `dynalogin-1.0.0/libdynalogin/datasources/odbc/odbc_ds.c`

ligne 115 a 132


    apr_status_t odbc_get_string(char **s, SQLHSTMT stmt,
                    SQLUSMALLINT col, apr_pool_t *pool)
    {
            SQLRETURN ret;
            SQLLEN len;

            if((*s = apr_pcalloc(pool, 128)) == NULL)
                    return APR_EGENERAL;
    
            ret = SQLGetData(stmt, col, SQL_C_CHAR, *s, 128, &len);
            if(!SQL_SUCCEEDED(ret))
                    return APR_EGENERAL;

            if (len == SQL_NULL_DATA)
                    return APR_SUCCESS;

            return APR_SUCCESS;
    }

