
# Filecoin Whitepaper
Источник: https://filecoin.io/filecoin.pdf
Комментарий переводчика: перевожу для себя, поэтому дословного перевода не ждите, только самая важная информация.

### Abstract

Filecoin - это децентрализованная сеть хранения. Система работает на блокчейне с собственным токеном (также называемым «Filecoin»), который добывают шахтеры, предоставляя хранилище клиентам. И наоборот, клиенты тратят Filecoin на привлечение шахтеров для хранения или распространения данных. Как и в случае с Bitcoin, шахтёры Filecoin конкурируют за майнинг блоков с значительными вознаграждениями, но кол-во Filecoin для майнеров пропорциональна их хранилищу, что напрямую обеспечивает полезную услугу для клиентов (в отличие от майнинга Bitcoin, полезность которой ограничена поддержкой консенсуса по цепочке). Это создает мощный стимул для шахтеров накапливать как можно больше хранилищ, а также сдавать их в аренду клиентам.

Сеть обеспечивает устойчивость, реплицируя и рассеивая контент, одновременно автоматически обнаруживая и восстанавливая ошибки репликации. Клиенты могут выбирать параметры репликации для защиты от разных моделей угроз. Сеть облачных хранилищ протокола также обеспечивает безопасность, поскольку контент зашифровывается от конца до конца на клиенте, а поставщики хранилища не имеют доступа к ключам дешифрования. Filecoin работает как стимулирующий слой поверх IPFS, который может обеспечить инфраструктуру хранения для любых данных. Это особенно полезно для децентрализации данных, создания и запуска распределенных приложений и реализации интеллектуальных контрактов.

Этот документ:
(a) представляет сеть Filecoin, дает обзор протокола и подробно рассматривает несколько компонентов.
(б) Формирует схемы децентрализованной сети хранения (DSN) и их свойства, а затем создает Filecoin как DSN.
(c) Внедряет новый класс схем доказательства хранения, называемых доказательством репликации, что позволяет доказать, что любая копия данных хранится в физически независимом хранилище.
(d) представляет новый полезный консенсус, основанный на последовательных доказательствах репликации и хранения в качестве меры весов.
(e) Формализует проверяемые рынки и строит два рынка - рынок хранения и рынок восстановления, которые определяют, как данные записываются и считываются из Filecoin, соответственно.
(f) Обсуждает варианты использования, подключения к другим системам и способы использования протокола.

#### Содержание
1. Введение
1.1 Базовые компоненты
1.2 Обзор протокола
1.3 Организация бумаги
2. Определение распределенного файлового хранилища
2.1 Отказоустойчивость
2.2 Свойства
3. Proof-of-Replication и Proof-of-Spacetime
3.1 Мотивация
3.2 Proof-of-Replication
3.3 Proof-of-Spacetime
3.4 PoRep и PoSt на практике
3.5 Использование в Filecoin
4. Filecoin: a DSN Construction
4.1 Настройки
4.2 Структура данных
4.3 Протокол
4.4 Гарантии и требования
5. Файловое хранилище и извлечение
5.1 Подтверждаемые рынки
5.2 Рынок хранения
5.3 Рынок извлечения/восстановлени
6. Useful Work Consensus
6.1 Мотивация
6.2 Filecoin консенсус
7. Умные контракты
7.1 Контракты в Filecoin
7.2 Интеграция с другими системами
8. Будущие работы
8.1 Продолжающаяся работа
8.2 Открытые вопросы
8.3 Доказательства и формальная проверка

#### Список рисунков
Эскиз протокола Filecoin
Иллюстрация протокола Filecoin
Иллюстрация основного механизма PoSt.Prove
Эскизы протоколов доказательств
Структуры данных в схеме DSN
Пример выполнения FileNin DSN
Описание протоколов Put и Get в FileNin DSN
Описание протокола управления в DSN Filecoin
Общий протокол для проверяемых рынков
Структуры данных заказов для рынков поиска и хранения
Подробный протокол рынка хранения
Протокол детального поискового рынка
Выборы лидеров в протоколе ожидаемого согласия

Закончил на 3-ей странице

### 1.Введение
Filecoin это протокол, который работает с помощью новых "доказательств", так как Proof-of-Spacetime, где блоки создаются майнерами, которые хранят данные. Filecoin это протокол, который предоставляет сервис сохранения и извлечения данных через сеть независимых поставщиков хранилищ. Протокол, который не полагается на какого-либо координатора, где: 
* (1) Клиенты платят за сохранение и за восстановление данных 
* (2) Майнеры-хранители получают токены за сохранение данных 
* (3) Майнеры-поставщики получают токены за выдачу данных.

#### 1.1 Базовые компоненты
Протокол Filecoin основывается на четырех компонентах.

1. Децентрализованная сеть хранения (DSN): We provide an abstraction for network of independent storage providers to offer storage and retrieval services (in Section 2). Later, we present the Filecoin protocol as an incentivized, auditable and verifiable DSN construction (in Section 4).
2. Новый Proofs-of-Storage: We present two novel Proofs-of-Storage (in Section 3): 
* (1) Proof-of-Replication allows storage providers to prove that data has been replicated to its own uniquely dedicated physical storage. Enforcing unique physical copies enables a verifier to check that a prover is not deduplicating multiple copies of the data into the same storage space; 
* (2) Proof-of-Spacetime allows storage providers to prove they have stored some data throughout a specified amount of time.
3. Verifiable Markets: We model storage requests and retrieval requests as orders in two decentralized verifiable markets operated by the Filecoin network (in Section 5). Verifiable markets ensure that payments are performed when a service has been correctly provided. We present the Storage Market and the Retrieval Market where miners and clients can respectively submit storage and retrieval orders.
4. Полезный Proof-of-Work : We show how to construct a useful Proof-of-Work based on Proof-of-Spacetime that can be used in consensus protocols. Miners do not need to spend wasteful computation to mine blocks, but instead must store data in the network.

#### 1.2 Обзор протокола
• The Filecoin protocol is a Decentralized Storage Network construction built on a blockchain and with a native token. Clients spend tokens for storing and retrieving data and miners earn tokens by storing and serving data.
• The Filecoin DSN handle storage and retrieval requests respectively via two verifiable markets: the Storage Market and the Retrieval Market. Clients and miners set the prices for the services requested and offered and submit their orders to the markets.
• The markets are operated by the Filecoin network which employs Proof-of-Spacetime and Proof-of-Replication to guarantee that miners have correctly stored the data they committed to store.
• Finally, miners can participate in the creations of new blocks for the underlining blockchain. The influence of a miner over the next block is proportional to the amount of their storage currently in use in the network.

A sketch of the Filecoin protocol, using nomenclature defined later within the paper, is shown in Figure 1 accompanied with an illustration in Figure 2.

#### 1.3 Организация документа
The remainder of this paper is organized as follows. We present our definition of and requirements for a theoretical DSNscheme in Section 2. In Section 3 we motivate, define, and present our Proof-of-Replication and Proof-of-Spacetime protocols, used within Filecoin to cryptographically verify that data is continuously 4stored in accordance with deals made. Section 4 describes the concrete instantiation of the Filecoin DSN, describing data structures, protocols, and the interactions between participants. Section 5 defines and describes the concept of Verifiable Markets, as well as their implementations, the Storage Market and Retrieval Market. Section 6 motivates and describes the use of the Proof-of-Spacetime protocol for demonstrating and evaluating a miner’s contribution to the network, which is necessary to extend the blockchain and assign the block reward. Section 7 provides a brief description of Smart Contracts within the Filecoin We conclude with a discussion of future work in Section 8.

{ здесь Рисунок 1 }
Эскиз протокола Filecoin

{ здесь Рисунок 2 } 
Илюстрация протокола Filecoin, которая показывает взаимодействие Клиента и Майнера. The Storage and Retrieval Markets shown above and below the blockchain, respectively, with time advancing from the Order Matching phase on the left to the Settlement phase on the right. Обратите внимание, микроплатежи могут быть сделаны для извлечения, клиент должен заблокировать средства по микротранзакциям

### 2 Definition of a Decentralized Storage Network
We introduce the notion of a Decentralized Storage Network (DSN) scheme. DSNs aggregate storage offered by multiple independent storage providers and self-coordinate to provide data storage and data retrieval to clients. Coordination is decentralized and does not require trusted parties: the secure operation of theses systems is achieved through protocols that coordinate and verify operations carried out by individual parties.
DSNs can employ different strategies for coordination, including Byzantine Agreement, gossip protocols, or CRDTs, depending on the requirements of the system. Later, in Section 4, we provide a construction for the Filecoin DSN.

Definition 2.1. A DSN scheme Π is a tuple of protocols run by storage providers and clients: (Put, Get, Manage)
* Put(data) → key: Clients execute the Put protocol to store data under a unique identifier key.
* Get(key) → data: Clients execute the Get protocol to retrieve data that is currently stored using key.
* Manage(): The network of participants coordinates via the Manage protocol to: control the available storage, audit the service offered by providers and repair possible faults. The Manage protocol is run by storage providers often in conjunction with clients or a network of auditors. (In the case where the Manage protocol relies on a blockchain, we consider the miners as auditors, since they verify and coordinate storage providers)

A DSN scheme Π must guarantee data integrity and retrievability as well as tolerate management and storage faults defined in the following sections.

#### 2.1 Fault tolerance
##### 2.1.1 Management faults
We define management faults to be byzantine faults caused by participants in the Manage protocol. A DSN scheme relies on the fault tolerance of its underlining Manage protocol. Violations on the faults tolerance assumptions for management faults can compromise liveness and safety of the system.

For example, consider a DSN scheme Π, where the Manage protocol requires Byzantine Agreement (BA) to audit storage providers. In such protocol, the network receives proofs of storage from storage providers and runs BA to agree on the validity of these proofs. If the BA tolerates up to f faults out of n total
nodes, then our DSN can tolerate f < n/2 faulty nodes. On violations of these assumptions, audits can be compromised.

##### 2.1.2 Storage faults
We define storage faults to be byzantine faults that prevent clients from retrieving the data: i.e. Storage Miners lose their pieces, Retrieval Miners stop serving pieces. A successful Put execution is (f, m)-tolerant if it results in its input data being stored in m independent storage providers (out of n total) and it can tolerate up to f byzantine providers. The parameters f and m depend on protocol implementation; protocol designers can fix f and m or leave the choice to the user, extending Put(data) into Put(data, f , m). A Get execution on stored data is successful if there are fewer than f faulty storage providers.

For example, consider a simple scheme, where the Put protocol is designed such that each storage provider stores all of the data. In this scheme m = n and f = m − 1. Is it always f = m − 1? No, some schemes can be designed using erasure coding, where each storage providers store a special portion of the data, such that x out of m storage providers are required to retrieve the data; in this case f = m − x.

#### 2.2 Properties
We describe the two required properties for a DSN scheme and then present additional properties required by the Filecoin DSN.

##### 2.2.1 Data Integrity
This property requires that no bounded adversary A can convince clients to accept altered or falsified data at the end of a Get execution.

Definition 2.2. A DSN scheme Π provides data integrity if: for any successful Put execution for some data d under key k, there is no computationally-bounded adversary A that can convince a client to accept d 0 , for d 0 6 = d at the end of a Get execution for identifier k.

##### 2.2.2 Retrievability
This property captures the requirement that, given our fault-tolerance assumptions of Π, if some data has been successfully stored in Π and storage providers continue to follow the protocol, then clients can eventually retrieve the data.

Definition 2.3. A DSN scheme Π provides retrievability if: for any successful Put execution for data under key, there exists a successful Get execution for key for which a client retrieves data.(This definition does not guarantee every Get to succeed: if every Get eventually returns data, then the scheme is fair.)

##### 2.2.3 Other Properties
DSNs can provide other properties specific to their application. We define three key properties required by the Filecoin DSN: public verifiability, auditability, and incentive-compatibility.

Definition 2.4. A DSN scheme Π is publicly verifiable if: for each successful Put, the network of storage providers can generate a proof that the data is currently being stored. The Proof-of-Storage must convince any efficient verifier, which only knows key and does not have access to data.

Definition 2.5. A DSN scheme Π is auditable, if it generates a verifiable trace of operation that can be checked in the future to confirm storage was indeed stored for the right duration of time.

Definition 2.6. A DSN scheme Π is incentive-compatible, if: storage providers are rewarded for successfully offering storage and retrieval service, or penalized for misbehaving, such that the storage providers’ dominant strategy is to store data.