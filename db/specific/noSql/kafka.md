# Apache Kafka

Kafka — это распределенный реплицированный журнал фиксации изменений (commit log). У Kafka’и нет концепции очередей, что сначала может показаться странным, учитывая, что его используют в качестве системы обмена сообщениями. Очереди долгое время были синонимом систем обмена сообщениями. Давайте для начала разберемся, что значит «распределенный, реплицированный журнал фиксации изменений»:

- Распределенный, поскольку Kafka развертывается как кластер узлов, как для устойчивости к ошибкам, так и для масштабирования
- Реплицированный, поскольку сообщения обычно реплицируются на нескольких узлах (серверах).
- Журнал фиксации изменений, потому что сообщения хранятся в сегментированных, append-only журналах, которые называются топиками. Эта концепция журналирования является основным уникальным преимуществом Kafka’и.

 Понимание журнала (и топика) и партиций являются ключом к пониманию Kafka’и. Итак, чем партиционированный журнал отличается от набора очередей? Давайте представим, как это выглядит.

![](../../../media/kaffka.png)

Вместо того, чтобы помещать сообщения в очередь FIFO и отслеживать статус этого сообщения в очереди, как это делает RabbitMQ, Kafka просто добавляет его в журнал, и на этом все.

Сообщение остается, вне зависимости от того, будет ли оно получено один или несколько раз. Удаляется оно в соответствии с политикой удерживания данных (retention policy, также называемый window time period). Каким же образом информация забирается из топика?

Каждый получатель отслеживает, где она находится в журнале: имеется указатель на последнее полученное сообщение и этот указатель называется адресом смещения. Получатели поддерживают этот адрес через клиентские библиотеки, и в зависимости от версии Kafka адрес сохраняется либо в ZooKeeper, либо в самой Kafka’е.

Отличительная особенность модели журналирования в том, что она мгновенно устраняет множество сложностей, касающихся состояния доставки сообщений и, что более важно для получателей, позволяет им перематывать назад, возвращаться и получать сообщения по предыдущему относительному адресу. Например, представьте, что вы разворачиваете сервис, который выставляет счета, учитывающие заказы, размещаемые клиентами. У службы случилась ошибка, и она неправильно рассчитывает все счета за 24 часа. С RabbitMQ в лучшем случае вам нужно будет как-то переопубликовать эти заказы только на сервисе счетов. Но с Kafka вы просто перемещаете относительный адрес для этого получателя на 24 часа назад.

Каждая партиция представляет собой отдельный файл, в котором гарантируется очередность сообщений. Это важно помнить: порядок сообщений гарантируется только в одной партиции. В дальнейшем это может привести к некоторому противоречию между потребностями в очередности сообщений и потребностями в производительности, поскольку производительность в Kafka также масштабируется партициями. Партиция не может поддерживать конкурирующих получателей, блок параллельности Kafka — это сама партиция. Поэтому, если нам нужны три получателя счетов, нам нужно как минимум три партиции(1 воркер на 1 партицию).

*Дополнительно:*

- [RabbitMQ против Kafka](https://habr.com/company/itsumma/blog/416629/)
- [Apache Kafka – мой конспект](https://habr.com/post/354486/)