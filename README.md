Session 4
Assignment 5
1.	Checksum
•	Definition:
	LocalFileSystem uses ChecksumFileSystem to do its work, and this class makes it easy to add checksumming to other (non-checksummed) filesystems, as ChecksumFileSystem is just a wrapper around FileSystem. 
	Returns the checksum information of a file.
	If an error is detected by ChecksumFileSystem when reading a file, it will call its reportChecksumFailure () method.
•	Importance:
	NameNode handles data node failures through checksums .Every data in NameNode has a record followed by a checksum and if the checksum does not match with the original then it reports data corrupted error.
	HDFS transparently checksums all data written to it and by default verifies checksums when reading data .A separate checksums created for every bytes of data(default is 512 bytes, because CRC-32 checksums is 4 bytes). Datanodes are responsible for verifying the data they receive before storing the data and checksums.It is possible to disable checksums by passing false to setverifychecksum() method on filesystem before using open() method to read file.
	In DataNode, Block Scanner tracks the list of blocks and verifies them to find any kind of checksum errors. Block Scanners use a throttling mechanism to reserve disk bandwidth on the datanode.
•	Usage of Checksum in Hadoop:
	General Syntax: hadoop fs -checksum URI
	Example:
	hadoop fs -checksum hdfs://nn1.text.txt/
	hadoop fs -checksum file://etc/hosts/
2.	Anatomy of file write to HDFS:
	Following are the important components involved in a file write.
	Data Queue: When client writes data, DFSOS splits into packets and writes into this internal queue.
	DataStreamer: The data queue is consumed by this component, which also communicates with name node for block allocation.
	ACK Queue: Packets consumed by DataStreamer are temporarily saved in an internal queue.
3.	HDFS handles failures during file write
	HDFS handles the failures occurring during file write by creating pipelines.
	In HDFS, when a file is opened for writing, a pipeline is created of rDataNodes(where r is the replication factor) to receive the copy of the files.
	If there is a datanode/network failure in the write pipeline, DFSClient will try to remove the failed datanode from the pipeline and then continue writing with the remaining datanodes. As a result, the number of datanodes in the pipeline is decreased.
	When the cluster size becomes extremely small, that is 3 or less nodes, the policy is to be set to NEVER in the default configuration file or disable the feature. 
	Here, NEVER will stop adding a new datanode in the file write.
