
import { useState, useCallback } from 'react';
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card';
import { Button } from '@/components/ui/button';
import { Upload, FileText, TrendingUp } from 'lucide-react';
import { motion } from 'framer-motion';

interface DataUploaderProps {
  onDataUpload: (data: any) => void;
}

const DataUploader = ({ onDataUpload }: DataUploaderProps) => {
  const [isDragOver, setIsDragOver] = useState(false);

  const generateSampleData = () => {
    const baseValue = 100;
    const trend = 0.1;
    const seasonality = 20;
    const noise = 10;
    
    const data = Array.from({ length: 365 }, (_, i) => {
      const date = new Date(2023, 0, i + 1);
      const trendValue = baseValue + (i * trend);
      const seasonalValue = seasonality * Math.sin((2 * Math.PI * i) / 365);
      const noiseValue = (Math.random() - 0.5) * noise;
      
      return {
        date: date.toLocaleDateString(),
        value: Math.max(0, trendValue + seasonalValue + noiseValue),
        timestamp: date.getTime()
      };
    });
    
    onDataUpload(data);
  };

  const handleDragOver = useCallback((e: React.DragEvent) => {
    e.preventDefault();
    setIsDragOver(true);
  }, []);

  const handleDragLeave = useCallback(() => {
    setIsDragOver(false);
  }, []);

  const handleDrop = useCallback((e: React.DragEvent) => {
    e.preventDefault();
    setIsDragOver(false);
    // File handling would go here
    generateSampleData(); // For demo purposes
  }, []);

  return (
    <div className="max-w-4xl mx-auto space-y-6">
      <Card className="bg-gray-800/50 border-gray-700 backdrop-blur-sm">
        <CardHeader className="text-center">
          <CardTitle className="text-2xl text-white">Upload Your Time Series Data</CardTitle>
          <CardDescription className="text-gray-300">
            Upload CSV files with date and value columns, or use our sample dataset
          </CardDescription>
        </CardHeader>
        <CardContent className="space-y-6">
          {/* Upload Area */}
          <motion.div
            whileHover={{ scale: 1.02 }}
            whileTap={{ scale: 0.98 }}
            className={`border-2 border-dashed rounded-lg p-12 text-center transition-all ${
              isDragOver
                ? 'border-purple-400 bg-purple-400/10'
                : 'border-gray-600 hover:border-gray-500'
            }`}
            onDragOver={handleDragOver}
            onDragLeave={handleDragLeave}
            onDrop={handleDrop}
          >
            <Upload className="w-16 h-16 text-gray-400 mx-auto mb-4" />
            <h3 className="text-lg font-semibold text-white mb-2">
              Drag and drop your CSV file here
            </h3>
            <p className="text-gray-400 mb-4">
              or click to browse files
            </p>
            <Button variant="outline" className="border-gray-600 text-gray-300 hover:bg-gray-700">
              Browse Files
            </Button>
          </motion.div>

          {/* Sample Data Options */}
          <div className="grid md:grid-cols-3 gap-4">
            {[
              {
                title: 'Sales Data',
                description: 'Monthly sales with seasonal patterns',
                icon: TrendingUp,
                trend: 'Upward trend with seasonality'
              },
              {
                title: 'Stock Prices',
                description: 'Daily stock price movements',
                icon: FileText,
                trend: 'Volatile with trend'
              },
              {
                title: 'Weather Data',
                description: 'Temperature readings over time',
                icon: TrendingUp,
                trend: 'Strong seasonal patterns'
              }
            ].map((sample, index) => (
              <motion.div
                key={index}
                whileHover={{ scale: 1.05 }}
                whileTap={{ scale: 0.95 }}
              >
                <Card 
                  className="bg-gray-700/50 border-gray-600 hover:border-purple-500 transition-all cursor-pointer"
                  onClick={generateSampleData}
                >
                  <CardContent className="p-4 text-center">
                    <sample.icon className="w-8 h-8 text-purple-400 mx-auto mb-2" />
                    <h4 className="font-semibold text-white mb-1">{sample.title}</h4>
                    <p className="text-sm text-gray-400 mb-2">{sample.description}</p>
                    <span className="text-xs text-purple-300">{sample.trend}</span>
                  </CardContent>
                </Card>
              </motion.div>
            ))}
          </div>

          <div className="text-center">
            <Button
              onClick={generateSampleData}
              className="bg-purple-600 hover:bg-purple-700 text-white px-8 py-3"
            >
              Use Sample Dataset
              <TrendingUp className="ml-2 w-4 h-4" />
            </Button>
          </div>
        </CardContent>
      </Card>
    </div>
  );
};

export default DataUploader;
