# Homework 4

## Optimising Storage

Take [this contract](https://gist.github.com/extropyCoder/6e9b5d5497b8ead54590e72382cdca24)
Use the [sol2uml tool](https://github.com/naddison36/sol2uml) to find out how many storage slots it is using.
By re ordering the variables, can you reduce the number of storage slots needed ?

Guide: https://github.com/naddison36/sol2uml

Install 

```
npm link sol2uml --only=production
```

Run

```
C:\homework4>sol2uml storage Store.sol -c Store
Generated svg file C:\homework4\Store.svg
```

60 slots occupied by Store.sol (before reordering)

![image](https://github.com/BigBangInfinity/Encode_ExpertSolidityBootcamp_Homework/assets/37957341/c1c07fb7-66ca-45bc-a70d-46a3c4978fc3)


Reordering in Store_new.sol

run 

```
\homework4>sol2uml storage Store_new.sol -c Store_new
Generated svg file C:\homework4\Store_new.svg
```

43 slots occupied

![image](https://github.com/BigBangInfinity/Encode_ExpertSolidityBootcamp_Homework/assets/37957341/23c906fb-814a-4d59-a710-0bdd203cf7af)


