The description on the document I selected is actually a wikipedia on RAG_systems as I thought it would be appropriate because of what we are building.


Five deep dive questions

Question 1: Why is the embedding dimensionality (d = embeddings.shape[1]) so important for initializing the FAISS index?
Answer: The FAISS index is a data structure designed to efficiently search through high-dimensional vectors. When we initialize an index like IndexFlatL2, we must tell it the exact dimension (e.g., 768 for all-distilroberta-v1) of the vectors it will store. This allows FAISS to allocate the correct amount of memory and build the internal structure needed to organize the vectors in a multi-dimensional space. If the dimension is wrong, the index cannot store or search the vectors correctly.

Question 2: What is faiss.IndexFlatL2 doing under the hood, and what are its trade-offs?
Answer: IndexFlatL2 performs an exhaustive search. This means that when you provide a query vector (the embedded question), it calculates the L2 distance (also known as Euclidean distance) between the query vector and every single vector in the index. It then sorts these distances to find the "nearest" or most similar vectors.

Advantage: It is guaranteed to find the absolute closest matches, making it 100% accurate.
Disadvantage: It is slow and computationally expensive for very large datasets (e.g., millions of vectors) because it checks every single point. For more advanced applications, one might use an approximate index (like IndexIVFFlat) that trades a tiny bit of accuracy for a massive speedup.

Question 3: What specific problem does chunk_overlap solve that simple splitting doesn't?
Answer: Simple splitting creates hard boundaries. Imagine a critical sentence is split exactly in half between chunk_1 and chunk_2. chunk_1 contains the beginning of the sentence, and chunk_2 contains the end. Neither chunk, on its own, contains the full idea. When a user asks a question about this idea, the retrieval system might only find one of these two incomplete chunks, leading to a poor or incorrect answer. chunk_overlap ensures that the full sentence will exist intact in at least one of the chunks (in this case, the complete sentence would be at the beginning of chunk_2), making it retrievable.

Question 4: Why is the prompt format Context: ... Question: ... Answer: so effective for the generator model?
Answer: This format is a form of instruction prompting. The generator model, google/flan-t5-small, has been fine-tuned on a wide variety of tasks presented as instructions. By clearly labeling "Context" and "Question," we are giving the model an unambiguous instruction: "Use the information provided in the Context section to answer the following Question." The "Answer:" label primes the model to begin its generation directly with the answer, without adding conversational filler. This structure constrains the model's output and focuses its attention on the relevant information, significantly improving the quality and relevance of the generated text.

Question 5: Why use two different models ?
Answer: The two models are specialized for different tasks.

sentence-transformers/all-distilroberta-v1 is an encoder-only model. It is specifically trained to create high-quality numerical representations (embeddings) of sentences where semantic similarity corresponds to closeness in vector space. Its strength is in understanding text.
google/flan-t5-small is an encoder-decoder model (text-to-text). It is designed to take a text prompt as input and generate a new, coherent text sequence as output. Its strength is in language production and following instructions. Using each model for its specialized purpose—one for retrieval and one for generation—is the core principle of a RAG system and yields far better results than trying to use a single model for both tasks.


Why chunck_size and chunk_overlap matters

smaller chunk sizes are more focused and make it easier for the program to find a precise match and there is reduced computational load. but also can have a loss of context.
larger chunk sizes provide the model with richer context to give back more coherent answers. but also has the drawback that there can be less precise answers.
chunk overlap helps with making sure that sentences are not cut off when breaking things down into chuncks so that text is not repeated and making it easier for the model to retireve information because if a sentence is cut the model it would make it very difficult to retirieve.

The quality of responses were good as they explain enough but do not go way too far in depth unless you as it to.
The only suggestion that I woud have for improvement is to remind students that they need to use a venv environment so that they can ceen thier computer clean.