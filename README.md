import asyncio
from concurrent.futures import ThreadPoolExecutor

class TraceResult:
    def __init__(self, data):
        self.data = data

class Processor:
    def force_flush(self) -> TraceResult:
        # Simulate some processing
        import random
        data = random.randint(1, 100)
        return TraceResult(data)

class TracerProvider:
    def __init__(self):
        self.processors = []

    def force_flush(self):
        return [processor.force_flush() for processor in self.processors]

    def force_flush_async(self):
        with ThreadPoolExecutor() as executor:
            return executor.submit(self.force_flush)

# Create a TracerProvider and assign Processor instances
tracer_provider = TracerProvider()
tracer_provider.processors = [Processor(), Processor()]

async def main():
    # Spawn a task for force_flush
    loop = asyncio.get_event_loop()
    future = loop.run_in_executor(None, tracer_provider.force_flush)
    results = await future

    # Print the results
    for result in results:
        print(result.data)

# Run the main function
asyncio.run(main())
