Changelog
=========


v0.14.1 (2017-09-09)
--------------------

- Fix typo in ``SQL_ENCODING`` config option mapping to SQLAlchemy parameter. Thanks dsully_!


v0.14.0 (2017-08-03)
--------------------

- Make ``declarative_base`` pass extra keyword arguments to ``sqlalchemy.ext.declarative.declarative_base``.
- Remove ``ModelBase.metaclass`` and ``ModelBase.metadata`` hooks for hoisting those values to ``declarative_base()``. Instead, pass optional ``metadata`` and ``metaclass`` arguments directly to ``declarative_base``. (**breaking change**)
- Replace broken ``declarative_base`` decorator usage with new decorator-only function, ``as_declarative``. Previously, ``@declarative_base`` only worked as a decorator when not "called" (i.e. ``@declarative_base`` worked but ``@declarative_base(...)`` failed).


v0.13.0 (2017-07-11)
--------------------

- Add ``ModelBase.__dict_args__`` attribute for providing arguments to ``ModelBase.to_dict``.
- Add ``adapters`` option to ``ModelBase.__dict_args__`` for mapping model value types to custom serializatoin handlers during ``ModelBase.to_dict()`` call.


v0.12.1 (2017-04-04)
--------------------

- Bump minimum requirement for pydash to ``v4.0.1``.
- Revert removal of ``Query.pluck`` but now ``pluck`` works with a deep path *and* path list (e.g. ``['a', 'b', 0, 'c']`` to get ``'value'`` in ``{'a': {'b': [{'c': 'value'}]}}`` which is something that ``Query.map`` doesn't support.


v0.12.0 (2017-04-03)
--------------------

- Bump minimum requirement for pydash to ``v4.0.0``. (**breaking change**)
- Remove ``Query.pluck`` in favor or ``Query.map`` since ``map`` can do everything ``pluck`` could. (**breaking change**)
- Rename ``Query.index_by`` to ``Query.key_by``. (**breaking change**)
- Rename ``callback`` argument to ``iteratee`` for ``Query`` methods:

  - ``key_by``
  - ``stack_by``
  - ``map``
  - ``reduce``
  - ``reduce_right``


v0.11.0 (2017-03-10)
--------------------

- Make ``SQLClient.save()`` update the declarative model registry whenever an model class isn't in it. This allows saving to work when a ``SQLClient`` instance was created before models have been imported yet.
- Make ``SQLClient.expunge()`` support multiple instances.
- Make ``SQLClient.save()`` and ``SQLQuery.save()`` handle saving empty dictionaries.


v0.10.0 (2017-02-13)
--------------------

- Add ``engine_options`` argument to ``SQLClient()`` to provide additional engine options beyond what is supported by the ``config`` argument.
- Add ``SQLClient.bulk_insert`` for performing an INSERT with a multi-row VALUES clause.
- Add ``SQLClient.bulk_insert_many`` for performing an ``executemany()`` DBAPI call.
- Add additional ``SQLClient.session`` proxy properties on ``SQLClient.<proxy>``:

  - ``bulk_insert_mappings``
  - ``bulk_save_objects``
  - ``bulk_update_mappings``
  - ``is_active``
  - ``is_modified``
  - ``no_autoflush``
  - ``preapre``

- Store ``SQLClient.models`` as a static ``dict`` instead of computed property but recompute if an attribute error is detected for ``SQLClient.<Model>`` to handle the case of a late model class import.
- Fix handling of duplicate base class names during ``SQLClient.models`` creation for model classes that are defined in different submodules. Previously, duplicate model class names prevented those models from being saved via ``SQLClient.save()``.


v0.9.1 (2017-01-12)
-------------------

- Fix handling of ``scopefunc`` option in ``SQLClient.create_session``.


v0.9.0 (2017-01-10)
-------------------

- Add ``session_class`` argument to ``SQLClient()`` to override the default session class used by the session maker.
- Add ``session_options`` argument to ``SQLClient()`` to provide additional session options beyond what is supported by the ``config`` argument.


v0.8.0 (2016-12-09)
-------------------

- Rename ``sqlservice.Query`` to ``SQLQuery``. (**breaking change**)
- Remove ``sqlservice.SQLService`` class in favor of utilizing ``SQLQuery`` for the ``save`` and ``destroy`` methods for a model class. (**breaking change**)
- Add ``SQLQuery.save()``.
- Add ``SQLQuery.destroy()``.
- Add ``SQLQuery.model_class`` property.
- Replace ``service_class`` argument with ``query_class`` in ``SQLClient.__init__()``. (**breaking change**)
- Remove ``SQLClient.services``. (**breaking change**)
- When a model class name is used for attribute access on a ``SQLClient`` instance, return an instance of ``SQLQuery(ModelClass)`` instead of ``SQLService(ModelClass)``. (**breaking change**)


v0.7.2 (2016-11-29)
-------------------

- Fix passing of ``synchronize_session`` argument in ``SQLService.destroy`` and ``SQLClient.destroy``. Argument was mistakenly not being used when calling underlying delete method.


v0.7.1 (2016-11-04)
-------------------

- Add additional database session proxy attributes to ``SQLClient``:

  - ``SQLClient.scalar -> SQLClient.session.scalar``
  - ``SQLClient.invalidate -> SQLClient.session.invalidate``
  - ``SQLClient.expire -> SQLClient.session.expire``
  - ``SQLClient.expire_all -> SQLClient.session.expire_all``
  - ``SQLClient.expunge -> SQLClient.session.expunge``
  - ``SQLClient.expunge_all -> SQLClient.session.expunge_all``
  - ``SQLClient.prune -> SQLClient.session.prune``

- Fix compatibility issue with pydash ``v3.4.7``.


v0.7.0 (2016-10-28)
-------------------

- Add ``core.make_identity`` factory function for easily creating basic identity functions from a list of model column objects that can be used with ``save()``.
- Import ``core.save``, ``core.destroy``, ``core.transaction``, and ``core.make_identity`` into make package namespace.


v0.6.3 (2016-10-17)
-------------------

- Fix model instance merging in ``core.save`` when providing a custom identity function.


v0.6.2 (2016-10-17)
-------------------

- Expose ``identity`` argument in ``SQLClient.save`` and ``SQLService.save``.


v0.6.1 (2016-10-17)
-------------------

- Fix bug where the ``models`` variable was mistakenly redefined during loop iteration in ``core.save``.


v0.6.0 (2016-10-17)
-------------------

- Add ``identity`` argument to ``save`` method to allow a custom identity function to support upserting on something other than just the primary key values.
- Make ``Query`` entity methods ``entities``, ``join_entities``, and ``all_entities`` return entity objects instead of model classes. (**breaking change**)
- Add ``Query`` methods ``model_classes``, ``join_model_classes``, and ``all_model_classes`` return the model classes belonging to a query.


v0.5.1 (2016-09-28)
-------------------

- Fix issue where calling ``<Model>.update(data)`` did not correctly update a relationship field when both ``<Model>.<relationship-column>`` and ``data[<relationship-column>]`` were both instances of a model class.


v0.5.0 (2016-09-20)
-------------------

- Allow ``Service.find_one``, ``Service.find``, and ``Query.search`` to accept a list of lists as the criterion argument.
- Rename ModelBase metaclass class attribute from ``ModelBase.Meta`` to ``ModelBase.metaclass``. (**breaking change**)
- Add support for defining the ``metadata`` object on ``ModelBase.metadata`` and having it used when calling ``declarative_base``.
- Add ``metadata`` and ``metaclass`` arguments to ``declarative_base`` that taken precedence over the corresponding class attributes set on the passed in declarative base type.
- Rename Model argument/attribute in ``SQLClient`` to ``__init__`` to ``model_class``. (**breaking change**)
- Remove ``Query.top`` method. (**breaking change**)
- Proxy ``SQLService.__getattr__`` to ``getattr(SQLService.query(), attr)`` so that ``SQLService`` now acts as a proxy to a query instance that uses its ``model_class`` as the primary query entity.
- Move ``SQLService.find`` and ``SQLService.find_one`` to ``Query``.
- Improve docs.


v0.4.3 (2016-07-11)
-------------------

- Fix issue where updating nested relationship values can lead to conflicting state assertion error in SQLAlchemy's identity map.


v0.4.2 (2016-07-11)
-------------------

- Fix missing ``before`` and ``after`` callback argument passing from ``core.save`` to ``core._add``.


v0.4.1 (2016-07-11)
-------------------

- Fix missing ``before`` and ``after`` callback argument passing from ``SQLService.save`` to ``SQLClient.save``.


v0.4.0 (2016-07-11)
-------------------

- Add support for ``before`` and ``after`` callbacks in ``core.save``, ``SQLClient.save``, and ``SQLService.save`` which are invoked before/after ``session.add`` is called for each model instance.


v0.3.0 (2016-07-06)
-------------------

- Support additional engine and session configuration values for ``SQLClient``.

  - New engine config options:

    - ``SQL_ECHO_POOL``
    - ``SQL_ENCODING``
    - ``SQL_CONVERT_UNICODE``
    - ``SQL_ISOLATION_LEVEL``

  - New session config options:

    - ``SQL_EXPIRE_ON_COMMIT``

- Add ``SQLClient.reflect`` method.
- Rename ``SQLClient.service_registry`` and ``SQLClient.model_registry`` to ``services`` and ``models``. (**breaking change**)
- Support ``SQLClient.__getitem__`` as proxy to ``SQLClient.__getattr__`` where both ``db[User]`` and ``db['User']`` both map to ``db.User``.
- Add ``SQLService.count`` method.
- Add ``Query`` methods:

  - ``index_by``: Converts ``Query.all()`` to a ``dict`` of models indexed by ``callback`` (`pydash.index_by <http://pydash.readthedocs.io/en/latest/api.html#pydash.collections.index_by>`_)
  - ``stack_by``: Converts ``Query.all()`` to a ``dict`` of lists of models indexed by ``callback`` (`pydash.group_by <http://pydash.readthedocs.io/en/latest/api.html#pydash.collections.group_by>`_)
  - ``map``: Maps ``Query.all()`` to a ``callback`` (`pydash.map_ <http://pydash.readthedocs.io/en/latest/api.html#pydash.collections.map_>`_)
  - ``reduce``: Reduces ``Query.all()`` through ``callback`` (`pydash.reduce_ <http://pydash.readthedocs.io/en/latest/api.html#pydash.collections.reduce_>`_)
  - ``reduce_right``: Reduces ``Query.all()`` through ``callback`` from right (`pydash.reduce_right <http://pydash.readthedocs.io/en/latest/api.html#pydash.collections.reduce_right>`_)
  - ``pluck``: Retrieves value of of specified property from all elements of ``Query.all()`` (`pydash.pluck <http://pydash.readthedocs.io/en/latest/api.html#pydash.collections.pluck>`_)
  - ``chain``: Initializes a chain object with ``Query.all()`` (`pydash.chain <http://pydash.readthedocs.io/en/latest/api.html#pydash.chaining.chain>`_)

- Rename ``Query`` properties: (**breaking change**)

  - ``model_classes`` to ``entities``
  - ``joined_model_classes`` to ``join_entities``
  - ``all_model_classes`` to ``all_entities``


v0.2.0 (2016-06-15)
-------------------

- Add Python 2.7 compatibility.
- Add concept of ``model_registry`` and ``service_registry`` to ``SQLClient`` class:

  - ``SQLClient.model_registry`` returns mapping of ORM model names to ORM model classes bound to ``SQLClient.Model``.
  - ``SQLService`` instances are created with each model class bound to declarative base, ``SQLClient.Model`` and stored in ``SQLClient.service_registry``.
  - Access to each model class ``SQLService`` instance is available via attribute access to ``SQLClient``. The attribute name corresponds to the model class name (e.g. given a ``User`` ORM model, it would be accessible at ``sqlclient.User``.

- Add new methods to ``SQLClient`` class:

  - ``save``: Generic saving of model class instances similar to ``SQLService.save`` but works for any model class instance.
  - ``destroy``: Generic deletion of model class instances or ``dict`` containing primary keys where model class is explicitly passed in. Similar to ``SQLService.destroy``.

- Rename ``SQLService.delete`` to ``destroy``. (**breaking change**)
- Change ``SQLService`` initialization signature to ``SQLService(db, model_class)`` and remove class attribute ``model_class`` in favor of instance attribute. (**breaking change**)
- Add properties to ``SQLClient`` class:

  - ``service_registry``
  - ``model_registry``

- Add properties to ``Query`` class:

  - ``model_classes``: Returns list of model classes used to during ``Query`` creation.
  - ``joined_model_classes``: Returns list of joined model classes of ``Query``.
  - ``all_model_classes``: Returns ``Query.model_classes`` + ``Query.joined_model_classes``.

- Remove methods from ``SQLService`` class: (**breaking change**)

  - ``query_one``
  - ``query_many``
  - ``default_order_by`` (default order by determination moved to ``Query.search``)

- Remove ``sqlservice.service.transaction`` decorator in favor of using transaction context manager within methods. (**breaking change**)
- Fix incorrect passing of ``SQL_DATABASE_URI`` value to ``SQLClient.create_engine`` in ``SQLClient.__init__``.


v0.1.0 (2016-05-24)
-------------------

- First release.


.. _dsully: https://github.com/dsully
