# qtiocompressor-qt5
Qt Solutions QtIOCompressor updated for Qt5.

QtIOCompressor is a QIODevice that compresses data streams.

The class works on top of a QIODevice subclass, compressing data
before it is written and decompressing it when it is read.

Since QtIOCompressor works on streams, it does not have to see the
entire data set before compressing or decompressing it. This can
reduce the memory requirements when working on large data sets.

This is a fork of the original Qt Solutions QtIOCompressor 2.3_1-opensource.
