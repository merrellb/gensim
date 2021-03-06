.. _intro:

============
Introduction 
============

Gensim is a Python framework designed to automatically extract semantic
topics from documents, as naturally and painlessly as possible.


Gensim aims at processing raw, unstructured digital texts ("*plain text*").
The unsupervised algorithms in `gensim`, such as **Latent Semantic Analysis**, **Latent Dirichlet Allocation** or **Random Projections**,
discover hidden (*latent*) semantic structure, based on word co-occurrence patterns within a corpus of training documents.
Once these statistical patterns are found, any plain text documents can be succinctly 
expressed in the new, semantic representation, and queried for topical similarity 
against other documents and so on.

If the previous paragraphs left you confused, you can read more about the `Vector 
Space Model <http://en.wikipedia.org/wiki/Vector_space_model>`_ and `unsupervised 
document analysis <http://en.wikipedia.org/wiki/Latent_semantic_indexing>`_ on Wikipedia.


.. _design:

Design objectives
------------------
 
`gensim` offers the following features:

* **Memory independence** -- there is no need for the whole training corpus to 
  reside fully in RAM at any one time (can process large, web-scale corpora).
* Efficient implementations for several popular vector space algorithms, 
  including **Tf-Idf**, distributed incremental **Latent Semantic Analysis**, 
  distributed incremental **Latent Dirichlet Allocation (LDA)** or **Random Projection**; adding new ones is easy (really!).
* I/O wrappers and converters around **several popular data formats**.
* **Similarity queries** for documents in their latent, topical representation.
 
Creation of `gensim` was motivated by a perceived lack of available, scalable software 
frameworks that realize topic modelling, and/or their overwhelming internal complexity (hail java!).
You can read more about the motivation in our `LREC 2010 workshop paper <http://nlp.fi.muni.cz/projekty/gensim/lrec2010_final.pdf>`_.
If you want to cite `gensim` in your own work, please refer to that article (`BibTeX <http://nlp.fi.muni.cz/projekty/gensim/bibtex_gensim.bib>`_).

The **principal design objectives** behind `gensim` are:

1. Straightforward interfaces and low API learning curve for developers. Good for prototyping.
2. Memory independence with respect to the size of the input corpus; all intermediate 
   steps and algorithms operate in a streaming fashion, accessing one document 
   at a time.


Availability
------------

.. seealso::

    See the :doc:`install <install>` page for more info on `gensim` deployment. 

Gensim is licensed under the OSI-approved `GNU LPGL license <http://www.gnu.org/licenses/lgpl.html>`_ 
and can be downloaded either from its `github repository <https://github.com/piskvorky/gensim/>`_
or from the `Python Package Index <http://pypi.python.org/pypi/gensim>`_. 


Core concepts   
-------------

The whole gensim package revolves around the concepts of :term:`corpus`, :term:`vector` and 
:term:`model`.

.. glossary::
    
    Corpus
        A collection of digital documents. This collection is used to automatically 
        infer structure of the documents, their topics etc. For
        this reason, the collection is also called a *training corpus*. The inferred 
        latent structure can be later used to assign topics to new documents, which did 
        not appear in the training corpus.
        No human intervention (such as tagging the documents by hand, or creating 
        other metadata) is required.
    
    Vector
        In the Vector Space Model (VSM), each document is represented by an 
        array of features. For example, a single feature may be thought of as a 
        question-answer pair:
        
        1. How many times does the word *splonge* appear in the document? Zero.
        2. How many paragraphs does the document consist of? Two.
        3. How many fonts does the document use? Five.
        
        The question is usually represented only by its integer id, so that the
        representation of a document becomes a series of pairs like ``(1, 0.0), (2, 2.0), (3, 5.0)``.
        If we know all the questions in advance, we may leave them implicit 
        and simply write ``(0.0, 2.0, 5.0)``.
        This sequence of answers can be thought of as a high-dimensional (in this case 3-dimensional)
        *vector*. For practical purposes, only questions to which the answer is (or
        can be converted to) a single real number are allowed. 
        
        The questions are the same for each document, so that looking at two 
        vectors (representing two documents), we will hopefully be able to make
        conclusions such as "The numbers in these two vectors are very similar, and 
        therefore the original documents must be similar, too". Of course, whether 
        such conclusions correspond to reality depends on how well we picked our questions.
        
    Sparse vector
        Typically, the answer to most questions will be ``0.0``. To save space,
        we omit them from the document's representation, and write only ``(2, 2.0), 
        (3, 5.0)`` (note the missing ``(1, 0.0)``).
        Since the set of all questions is known in advance, all the missing features
        in a sparse representation of a document can be unambiguously resolved to zero, ``0.0``.
        
        Gensim is specific in that it doesn't prescribe any specific corpus format;
        a corpus is anything that, when iterated over, successively yields these sparse vectors.
        For example, `set([(2, 2.0), (3, 5.0)], ([0, -1.0], [3, -1.0]))` is a trivial 
        corpus of two documents, each with two non-zero `feature-answer` pairs.
         
        
    
    Model
        For our purposes, a model is a transformation from one document representation
        to another (or, in other words, from one vector space to another). 
        Both the initial and target representations are
        still vectors -- they only differ in what the questions and answers are.
        The transformation is automatically learned from the traning :term:`corpus`, without human
        supervision, and in hopes that the final document representation will be more compact
        and more useful: with similar documents having similar representations.
 
.. seealso::

    For some examples on how this works out in code, go to :doc:`tutorials <tutorial>`.
