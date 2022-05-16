# DDL-1
DDL for a database project
create table investor(
    ID int primary key
      check(id >= 111111111 and id <= 999999999),
    Name varchar(100) not null,
    Email varchar(100) unique
    check (Email like '%@%'),
    Registration_date date not null,
    BirthDate date not null
        check(year(BirthDate) < 2004)
);
-- Time that has passed from registration date cant be counted,
-- therefore we cant check if 1 year/3 month has been passed from registration date
--Thus we cant check investor status(Beginner, premium, none)


create table PremiumInvestor(
     ID int foreign key references investor(ID) on delete cascade,
     financialDestination varchar(100),
     primary key(ID)
);

create table Economist(
    ID int foreign key references  PremiumInvestor(ID) on delete cascade ,
    sector varchar(100) ,
    primary key (ID)
);

create table Lawyer(
    ID int foreign key references  PremiumInvestor(ID) on delete cascade,
    sector varchar(100) ,
    primary key (ID)
);

create table BeginnerInvestor(
     ID int foreign key references investor(ID) on delete cascade,
     ecoId int foreign key references Economist(ID),
     primary key (ID)
);

Create table company(
    Symbol varchar(100) primary key ,
    Location varchar(100) not null,
    founded int not null,
    Sector varchar(100)
);

-- In company we cant check if every company has a stock ,
-- because enforcement between more than one relation is impossible in DDL.


create table stock(
    tDate date,
    symbol varchar(100) foreign key references company(symbol) on delete cascade,
    price float not null,
    PRIMARY KEY (tDate,symbol)
);

create table Trans(
    investorID int foreign key references investor(ID) on delete cascade,
    TransferDate date,
    TransferAmount int
        check(TransferAmount >= 1000),
    primary key(investorID,TransferDate)
);

create table Rivalry(
    company1 varchar(100) foreign key references company(Symbol), --on delete cascade
    company2 varchar(100)foreign key references company(Symbol) on delete cascade,
    rivalryReason varchar(100) ,
    primary key (company1, company2)
);
-- In order to ensure that rivalry between A and B will not be double documented we will check if A > B
--cant be checked in DDL.

create table CheckedBy(
    TransactionDate date ,
    investorTransactionID int,
    lawyerID int foreign key references Lawyer(ID),
    lawyerDecision varchar(100) not null ,
    foreign key(investorTransactionID,TransactionDate) references Trans(investorID,TransferDate) on delete cascade,
    primary key(investorTransactionID,TransactionDate)
);

create table appearsINblog(
    company1 varchar(100) ,
    company2 varchar(100) ,
    economistId int foreign key references Economist(ID) on delete cascade,
    foreign key(company1,company2) references Rivalry(company1,company2) ,--on delete cascade
    BlogSummary varchar(100) not null ,
    primary key (company1, company2)
);

create table buying(
    investorId int foreign key references investor(ID),-- on delete cascade
    StockDate date,
    StockSymbol varchar(100),
    StockAmount int
        check (stockAmount > 0),
    foreign key(StockDate,StockSymbol) references stock(tdate,symbol) on delete cascade,
    primary key (investorId,StockDate,StockSymbol)
);


